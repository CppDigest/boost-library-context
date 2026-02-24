# #200 - Dereferencing std::vector::end using boost::process::limit_handles flag on Windows [Closed]

> Username: Breakwell  
> Created at: 2021-02-25 15:43:07 UTC  
> Updated at: 2024-11-08 15:43:43 UTC  
> Closed at: 2024-11-08 13:48:53 UTC  
> Url: https://github.com/boostorg/process/issues/200  

I've noticed while using ```boost::process::limit_handles``` that the code seems to be attempting to dereference std::vector::end which ultimately ends up with undefined behavior. We can see [here](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/windows/handles.hpp#L142)  
  
```  
        auto all_handles = get_handles();  
        foreach_used_handle(exec,  
                [&](::boost::winapi::HANDLE_ handle)  
                {  
                    auto itr = std::find(all_handles.begin(), all_handles .end(), handle);  
                    ::boost::winapi::DWORD_ flags = 0u;  
                    if (itr != all_handles.end())  
                        *itr = ::boost::winapi::INVALID_HANDLE_VALUE_;  
                    else if ((::boost::winapi::GetHandleInformation(*itr, &flags) != 0)  
                            &&((flags & ::boost::winapi::HANDLE_FLAG_INHERIT_) == 0)) //it is NOT inherited anyhow, so ignore too  
                        *itr = ::boost::winapi::INVALID_HANDLE_VALUE_;  
                });  
  
        auto part_itr = std::partition(all_handles.begin(), all_handles.end(),  
                                       [](::boost::winapi::HANDLE_ handle) {return handle != ::boost::winapi::INVALID_HANDLE_VALUE_;});  
  
        all_handles.erase(part_itr, all_handles.end()); //remove invalid handles  
        handles_with_inherit_flag = std::move(all_handles);  
  
        for (auto handle : handles_with_inherit_flag)  
            ::boost::winapi::SetHandleInformation(handle, ::boost::winapi::HANDLE_FLAG_INHERIT_, 0);  
```  
If the ```iterator != all_handles.end()``` then it was found and set the value to ```::boost::winapi::INVALID_HANDLE_VALUE_```. Otherwise dereference the iterator into GetHandleInformation, however the iterator will equal all_handles.end(). It also seems that if this is the case there's no point in setting ```itr``` to ```::boost::winapi::INVALID_HANDLE_VALUE_``` as it already is not present in all_handles list and therefore ```::boost::winapi::SetHandleInformation``` won't be called on it anyway. It seems the additional ```else if``` is redundant?  
  
Ordinarily if this code is being called you would expect at least 1 handle to be present (for the pipe) in the calling process and therefore wouldn't normally run into this error but I am also seeing it's possible for get_handles() to return nothing and this then seems to cause an access violation.

---

## Comment 1

> Username: tomix86  
> Created at: 2021-03-01 05:57:51 UTC  
> Url: https://github.com/boostorg/process/issues/200#issuecomment-787667059  

We've also ran into this issue.  
  
I guess the author's intention was to apply the filtering, and thus remove all non-inherited handles, to the original list of handles the process has open (`all_handles`), not to the handle being evaluated in `foreach_used_handle()` iteration.

---

## Comment 2

> Username: Breakwell  
> Created at: 2021-03-08 11:12:45 UTC  
> Updated at: 2021-03-08 11:28:34 UTC  
> Url: https://github.com/boostorg/process/issues/200#issuecomment-792683027  

Looking at it more closely if the process PID exceeds 65535 (USHRT_MAX) then the [get_handles](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/windows/handles.hpp#L27) call will not return any handles for the current process. As it's undocumented it's hard to hunt down but it looks like the ```NtQuerySystemInformation``` call is limited to PIDs in the range of an unsigned short. To fix this instead of ```SYSTEM_HANDLE_INFORMATION``` we would need to consider using ```SYSTEM_HANDLE_INFORMATION_EX``` to query the system handles. At the moment even if we fix the dereference issue the code would still not reliably limit the handles.  
  
I think it would be safer to possibly use UpdateProcThreadAttribute() (at least for vista +) as suggested [originally](https://github.com/klemens-morgenstern/boost-process/pull/196/files#r280065110). It seems a bit unnecessary to iterate over all the system handles in order to limit the handles inherited by a process when UpdateProcThreadAttribute() is supported.

---

## Comment 3

> Username: wizardsd  
> Created at: 2023-12-06 11:38:00 UTC  
> Url: https://github.com/boostorg/process/issues/200#issuecomment-1842700175  

Please fix at least the undefined behavior, it's been 2 years already. "65535+ PID" is another issue and must be fixed seperately.

---

## Comment 4

> Username: tomatolog  
> Created at: 2024-11-04 18:23:32 UTC  
> Url: https://github.com/boostorg/process/issues/200#issuecomment-2455411904  

got the same crash on starting process on Windows like  
```  
struct Window_t  : boost::process::detail::handler_base  
{  
    // this function will be invoked at child process constructor before spawning process  
    template <class WindowsExecutor>  
    void on_setup ( WindowsExecutor & e ) const  
    {  
        e.creation_flags = boost::winapi::CREATE_NEW_CONSOLE_;  
    }  
};  
  
std::unique_ptr<boost::process::child> pBuddy;  
std::error_code tErrorCode;  
Window_t tWnd;  
pBuddy.reset ( new boost::process::child ( sCmd, ( boost::process::std_out & boost::process::std_err ) > *g_pPipe, tWnd, boost::process::limit_handles, boost::process::error ( tErrorCode ) ) );  
```  
  
the fix that helps me is  
```  
--- boost_1_82_0\boost\process\detail\windows\handles.hpp  
+++ boost_1_82_0\boost\process\detail\windows\handles.hpp  
@@ -136,12 +136,14 @@  
         auto all_handles = get_handles();  
         foreach_used_handle(exec,  
                 [&](::boost::winapi::HANDLE_ handle)  
                 {  
                     auto itr = std::find(all_handles.begin(), all_handles .end(), handle);  
                     ::boost::winapi::DWORD_ flags = 0u;  
+                    if (itr == all_handles.end())  
+                        return;  
                     if (itr != all_handles.end())  
                         *itr = ::boost::winapi::INVALID_HANDLE_VALUE_;  
                     else if ((::boost::winapi::GetHandleInformation(*itr, &flags) != 0)  
                             &&((flags & ::boost::winapi::HANDLE_FLAG_INHERIT_) == 0)) //it is NOT inherited anyhow, so ignore too  
                         *itr = ::boost::winapi::INVALID_HANDLE_VALUE_;  
                 });  
  
```

---

## Comment 5

> Username: Breakwell  
> Created at: 2024-11-08 15:43:42 UTC  
> Url: https://github.com/boostorg/process/issues/200#issuecomment-2465077152  

Raised: https://github.com/boostorg/process/issues/428 to keep track of the limits of limiting handles on systems with large PID values. As this was not resolved in [#424](https://github.com/boostorg/process/pull/424)
