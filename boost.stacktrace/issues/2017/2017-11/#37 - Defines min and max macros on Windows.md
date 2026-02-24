# #37 - Defines min and max macros on Windows [Closed]

> Username: peetw  
> Created at: 2017-11-09 14:50:37 UTC  
> Updated at: 2017-12-04 10:19:04 UTC  
> Closed at: 2017-11-24 20:35:24 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/37  

Essentially, the stacktrace module ([eventually](https://github.com/boostorg/winapi/blob/develop/include/boost/winapi/basic_types.hpp#L21)) includes the `windows.h` header, which then defines macros for min and max, which then conflict with the `std::min()`, `std::min()` and `std::numeric_limits<T>::min/max()` functions, resulting in compilation errors.  
  
This issue was also observed/fixed for the ASIO module: https://svn.boost.org/trac10/ticket/3901  
  
A workaround for now is to just `#define NOMINMAX` when building the project, however it would be much better if the stacktrace module defined this itself. Perhaps this could be defined in a new `detail/config.hpp` file ([similarly to ASIO](https://github.com/boostorg/asio/blob/develop/include/boost/asio/detail/config.hpp#L906))?

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-11-24 20:35:24 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/37#issuecomment-346893232  

You should probably report that issue to the https://github.com/boostorg/winapi module to get the fix for all the Boost libraries that use it, rather than just for the Stacktrace library.  
  
P.S.: It includes `windows.h` only if the `BOOST_USE_WINDOWS_H` macro is defined, so I actually do not see a problem here.

---

## Comment 2

> Username: Lastique  
> Created at: 2017-11-29 21:10:14 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/37#issuecomment-347997174  

Boost.WinAPI cannot define `NOMINMAX` because it's a user-configurable macro. It affects Windows SDK, which can be included externally, so defining it in Boost.WinAPI is not even effective.  
  
The right way to deal with `min`/`max` is to write library code to prevent their expansion. When calling `min`/`max`, enclose these names in parenthesis:  
  
```  
(std::min)(x, y);  
(std::numeric_limits< int >::max)();  
```  
  
When defining `min`/`max` functions, use `BOOST_PREVENT_MACRO_SUBSTITUTION`:  
  
```  
X min BOOST_PREVENT_MACRO_SUBSTITUTION (X a, X b)  
{  
}  
```  
  
I think, we had these tricks in our guidelines somewhere, but I can't find now.

---

## Comment 3

> Username: peetw  
> Created at: 2017-11-30 10:29:46 UTC  
> Updated at: 2017-11-30 10:33:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/37#issuecomment-348147710  

After looking into this in more detail, it looks like the problem does actually lie with the stacktrace module after all (the winapi module is performing as expected).  
  
When including `<boost/stacktrace.hpp>` on Windows and compiling with MSVC, `windows.h` is unconditionally included in `<boost/stacktrace/detail/frame_msvc.ipp>` (see [this line](https://github.com/boostorg/stacktrace/blob/develop/include/boost/stacktrace/detail/frame_msvc.ipp#L21)).  
  
Therefore, I would propose the following fix:  
  
    #ifdef BOOST_STACKTRACE_NO_NOMINMAX  
    #  include <windows.h>  
    #else  
    #  define NOMINMAX  
    #  include <windows.h>  
    #  undef NOMINMAX  
    #endif

---

## Comment 4

> Username: peetw  
> Created at: 2017-12-03 18:41:01 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/37#issuecomment-348804309  

As an example, when not using the boost stacktrace module, the following minimal code compiles OK and correctly prints `1` using the MSVC compiler on Windows:  
  
    // System/stdlib  
    #include <algorithm>  
    #include <iostream>  
  
    int main()  
    {  
        std::cout << std::max(-1, 1);  
    }  
  
However, including the boost stacktrace module (`v1.65.1`) causes the code to not compile due to the definition of the `min`/`max` macros:  
  
    // System/stdlib  
    #include <algorithm>  
    #include <iostream>  
  
    // Third-party  
    #include <boost/stacktrace.hpp>  
  
    int main()  
    {  
        std::cout << std::max(-1, 1); // MSVC errors C2059 and C2589  
    }  
  
Adding the fix described in the previous comment to `<boost/stacktrace/detail/frame_msvc.ipp>` solves the issue and the code will then compile.  
  
In cases where the user is already using the `min`/`max` macros from `<windows.h>`, including the fix described above does not affect the user at all - the following code will still compile:  
  
    // System/stdlib  
    #include <iostream>  
    #include <windows.h>  
  
    // Third-party  
    #include <boost/stacktrace.hpp>  
  
    int main()  
    {  
        std::cout << max(-1, 1);  
    }  
  
I will admit that this does depend on the order of the includes (the stacktrace module must be included after `windows.h`). However, if for some reason it can't be, the `BOOST_STACKTRACE_NO_NOMINMAX` can be defined, i.e. the following will also compile:  
  
    // Third-party  
    #define BOOST_STACKTRACE_NO_NOMINMAX  
    #include <boost/stacktrace.hpp>  
  
    // System/stdlib  
    #include <iostream>  
    #include <windows.h>      
  
    int main()  
    {  
        std::cout << max(-1, 1);  
    }  
  
However, this should be the much rarer, and easier to document case, as opposed to the current situation.  
  
Also note that I'm not particularly attached to the exact implementation of the proposed fix, I'm just using it as an example :)  
  
Is this something that would be worth submitting a PR for?

---

## Comment 5

> Username: Lastique  
> Created at: 2017-12-03 19:27:37 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/37#issuecomment-348807524  

@peetw IMHO, if you're having the problem with `min`/`max` just in your (user's) code then it is you who should be defining `NOMINMAX` when compiling the code. The library should only be modified if it itself has the problem with these macros, and in this case it should employ one of the workarounds I posted above. `BOOST_STACKTRACE_NO_NOMINMAX` is not the solution for the reasons I (and you) have outlined.

---

## Comment 6

> Username: peetw  
> Created at: 2017-12-04 10:19:04 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/37#issuecomment-348919255  

@Lastique It looks like we're going to have to agree to disagree on that - personally I don't think third-party libraries should re-define standard library functions by default.  
  
I know I can work around it (and I currently do, by placing def/undef `NOMINMAX` around the `#include <boost/stacktrace.hpp>` statement), but this should at least be documented in the stacktrace documentation to help future users. Perhaps a note could be added to the [Configuration and Build](https://github.com/boostorg/stacktrace/blob/develop/doc/stacktrace.qbk#L283) section?  
  
Finally, don't get me wrong, I am very grateful for the work you guys have put in to get this released - getting stacktraces in C++ on Windows was always a pain, so thank you! :)
