# #302 - Exception state corruption in 1.88 [Closed]

> Username: NomAnor  
> Created at: 2025-05-08 10:04:08 UTC  
> Updated at: 2025-05-25 14:34:41 UTC  
> Closed at: 2025-05-25 14:34:41 UTC  
> Url: https://github.com/boostorg/context/issues/302  

Currently on Arch Linux, the nix client constantly closes the connection to the nix daemon because of an "exception" as documented at https://gitlab.archlinux.org/archlinux/packaging/packages/nix/-/issues/17.  
The problem is caused by `std::uncaught_exceptions()` returning a negative value.  
  
I also filed a bug at nix (https://github.com/NixOS/nix/issues/13145) because I was not sure where the error is located. But I now thing it's in boost.  
  
With `git bisect` I traced this behaviour to the commit https://github.com/boostorg/context/commit/53b175512c48c3cab11ba914f9e895c20ab5100d in boost 1.88.  
  
I managed to create a producer  
```c++  
#include <iostream>  
#include <boost/context/fiber.hpp>  
  
namespace ctx=boost::context;  
  
int main()  
{  
    {  
        ctx::fiber f([] (ctx::fiber&& sink) {  
            std::cout << "Test" << std::endl;  
            sink = std::move(sink).resume();  
            return std::move(sink);  
        });  
        f = std::move(f).resume();  
        std::cout << "uncaught: " << std::uncaught_exceptions() << std::endl;  
    }  
    std::cout << "uncaught: " << std::uncaught_exceptions() << std::endl;  
    return 0;  
}  
  
```  
  
This results in the following output (I added some debug print in `fiber_fcontext.hpp`):  
```  
manage_exception_state(): 0, 0  
Test  
manage_exception_state(): 0, 0  
~manage_exception_state() before: 0, 0  
~manage_exception_state() after: 0, 0  
uncaught: 0  
~manage_exception_state() before: 0, 1  
!!!!!!!!!!!!!!  
~manage_exception_state() after: 0, 0  
!!!!!!!!!!! forced unwind  
uncaught: -1  
````  
  
As far as I undertand it, the `manage_exception_state` class reverts the libstdc++ internal state while forced stack unwinding when a fiber is cleaned up in it's destructor. This corrupts the internal exception counter, getting negative when the counter is decremented in the next catch in the entry function.  
  
These are my debug prints:  
```c++  
manage_exception_state() {  
    auto const globals = __cxa_get_globals();  
    std::cout << "manage_exception_state(): " << globals->caughtExceptions << ", " << globals->uncaughtExceptions << std::endl;  
    exception_state_ = *globals;  
}  
~manage_exception_state() {  
    auto globals = __cxa_get_globals();  
    std::cout << "~manage_exception_state() before: " << globals->caughtExceptions << ", " << globals->uncaughtExceptions << std::endl;  
    if (globals->uncaughtExceptions != exception_state_.uncaughtExceptions) {  
        std::cout << "!!!!!!!!!!!!!!" << std::endl;  
    }  
    *globals = exception_state_;  
    std::cout << "~manage_exception_state() after: " << globals->caughtExceptions << ", " << globals->uncaughtExceptions << std::endl;  
}
