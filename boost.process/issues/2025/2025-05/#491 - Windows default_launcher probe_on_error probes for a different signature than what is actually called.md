# #491 - Windows default_launcher probe_on_error probes for a different signature than what is actually called [Closed]

> Username: rlenhardt  
> Created at: 2025-05-14 15:16:17 UTC  
> Updated at: 2026-01-20 20:46:08 UTC  
> Closed at: 2025-06-20 16:37:09 UTC  
> Url: https://github.com/boostorg/process/issues/491  

See https://github.com/boostorg/process/blob/15555b966440e86a8b7f64be97fd3a41e409fdd0/include/boost/process/v2/windows/default_launcher.hpp#L112  
  
probe_on_error checks for a method that takes a **std::error** and returns a boost::system::error but the calls to on_error will pass a boost::system::error and return void.  
  
Workaround: add a dummy method with the probe signature to satisfy the compile time check, and add another method with the called signature:  
  
```  
#include <boost/process/v2/process.hpp>  
namespace bp = boost::process::v2;  
namespace bfs = bp::filesystem;  
#include <iostream>  
struct print_error  
{  
    template<typename Launcher>  
    boost::system::error_code on_error(Launcher& launcher, const bfs::path& executable, std::wstring& cmd_line, std::error_code& ec)  
    {  
        // will never be called but is needed to make the probe_on_error code compile  
        return ec;  
    }  
    template<typename Launcher>  
    void on_error(Launcher& launcher, const bfs::path& executable, std::wstring& cmd_line,  
                                       boost::system::error_code const& ec)  
    {  
        // will actually be called  
        std::wcerr << "Could not launch " << executable << std::endl << " cmdline: " << cmd_line << std::endl;  
        return;  
    }  
};  
  
#include <boost/asio/io_context.hpp>  
int main()  
{  
    asio::io_context ctx;  
    try {  
        bp::process(ctx,"notthere.exe",{},print_error{});  
    catch(...) {  
        std::cout << "Whoops"<<std::endl;  
    }  
    return 0;  
}  
  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-05-14 15:18:43 UTC  
> Url: https://github.com/boostorg/process/issues/491#issuecomment-2880638248  

Thanks for reporting, will fix asap.

---

## Comment 2

> Username: rlenhardt  
> Created at: 2026-01-10 17:24:17 UTC  
> Url: https://github.com/boostorg/process/issues/491#issuecomment-3733253940  

Sorry to come back to this late, but it is still not fixed. The probe and the call use different signatures, here is the call:  
https://github.com/boostorg/process/blob/2a41d0a0dcef77ff362c1e3a7cc88b47191ff9d2/include/boost/process/v2/windows/default_launcher.hpp#L102  
There is a `return` missing in the implementation of `invoke_on_error`, thus the call will not compile even though the probe detected a signature with return type `error_code`.  
  
So there is still two signatures needed to make use of `on_error` one for the probe and one for the call. I don't know which one do you intend to expose, the error_code returning or the void one, i guess the void one would be ok for my purposes of printing the error_code on failure to launch. Do you intend users of this on_error function to be able to change the returned error code? Or even try to do the launch on failure in some other way in this function and return success?

---

## Comment 3

> Username: krwalker  
> Created at: 2026-01-20 20:45:53 UTC  
> Updated at: 2026-01-20 20:46:08 UTC  
> Url: https://github.com/boostorg/process/issues/491#issuecomment-3774867790  

Does your recently added test show `on_success` working? #540 has changes that address it not getting called because of the return type.
