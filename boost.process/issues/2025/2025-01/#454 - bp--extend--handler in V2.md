# #454 - bp::extend::handler in V2? [Closed]

> Username: db4  
> Created at: 2025-01-18 09:47:05 UTC  
> Updated at: 2025-02-26 14:59:53 UTC  
> Closed at: 2025-02-26 14:59:51 UTC  
> Url: https://github.com/boostorg/process/issues/454  

For V1 I used the following trick to kill a child Linux process when its parent dies:  
```C++  
struct ExecHandler : bp::extend::handler  
{  
    template<typename Executor>  
    void on_exec_setup (Executor &) const  
    {  
        (void)prctl(PR_SET_PDEATHSIG, SIGTERM);  
    }  
};  
...  
    bp::child p(  
        ExecHandler(),  
        ...  
    );  
```  
Is there anything similar to `bp::extend::handler` in V2?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-01-20 16:57:02 UTC  
> Url: https://github.com/boostorg/process/issues/454#issuecomment-2602898693  

Similar code, just without the base-class:  
  
```cpp  
  
struct ExecHandler   
{  
  
    error_code on_exec_setup(posix::default_launcher & /*launcher*/, const filesystem::path & /* exe*/, const char * const * /* argv*/)  
   {  
              (void)prctl(PR_SET_PDEATHSIG, SIGTERM);  
   }  
  
};  
  
process::v2::process p{..., ExecHandler{}};

---

## Comment 2

> Username: db4  
> Created at: 2025-01-21 07:59:35 UTC  
> Url: https://github.com/boostorg/process/issues/454#issuecomment-2603898437  

@klemens-morgenstern, thanks a lot! If you don't mind, I have a similar question regarding Windows. I used to achieve the same effect of killing a process when its parent dies with  
```C++  
            bp::group g;  
            bp::child p(  
                ...  
                g,  
                io_service  
            );  
#ifdef _WIN32  
            namespace bpd = bp::detail;  
            bpd::api::workaround::JOBOBJECT_EXTENDED_LIMIT_INFORMATION_ info;  
            auto h = g.native_handle();  
            if (!bpd::api::workaround::query_information_job_object(  
                h,  
                bpd::api::workaround::JobObjectExtendedLimitInformation_,  
                static_cast<void*>(&info),  
                sizeof(info),  
                nullptr)) {  
                bpd::throw_last_error("QueryInformationJobObject() failed");  
            }  
            info.BasicLimitInformation.LimitFlags |= JOB_OBJECT_LIMIT_KILL_ON_JOB_CLOSE;  
            if (!bpd::api::workaround::set_information_job_object(  
                h,  
                bpd::api::workaround::JobObjectExtendedLimitInformation_,  
                static_cast<void*>(&info),  
                sizeof(info))) {  
                bpd::throw_last_error("SetInformationJobObject() failed");  
            }  
#endif  
```  
How to do this with V2? With `bp::group` no longer existing I don't see how to include a process into a group and get the group's handle.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-01-21 14:08:16 UTC  
> Url: https://github.com/boostorg/process/issues/454#issuecomment-2604835626  

I did not add the group to v2 because there are too many differences between the windows & linux implementation. And it's simple enough, you just got a bit of boilerplate re HANDLEs and error handling.  
  
The whole `api::workaround` stuff is also moot, because the   
  
You would need to use the job object directly:  
  
```cpp  
#include <windows.h>   
#include <boost/process/windows/creation_flag.hpp>  
  
auto h = ::CreateJobObjectW(nullptr, nullptr); // don't forget the CloseHandle  
  
  
bp::process proc(..., boost::process::windows::create_breakaway_from_job);  
  
AssignProcessToJobObject(h, proc.native_handle()); // error handling...  
  
// now do with the job object `h` what you have above  
```

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2025-02-26 14:59:51 UTC  
> Url: https://github.com/boostorg/process/issues/454#issuecomment-2685326538  

I'm assuming this does it, please reopen or create a new issue if you run into problems.
