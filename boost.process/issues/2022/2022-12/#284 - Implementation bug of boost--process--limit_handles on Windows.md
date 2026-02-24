# #284 - Implementation bug of boost::process::limit_handles on Windows [Open]

> Username: bambu123  
> Created at: 2022-12-15 01:22:33 UTC  
> Updated at: 2023-06-16 11:10:10 UTC  
> Url: https://github.com/boostorg/process/issues/284  

From "boost/process/detail/windows/handles.hpp", there are these codes.  
  
```  
    template<typename Executor>  
    void on_setup(Executor & exec) const  
    {  
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
    }  
```  
  
See: 'if  (itr != all_handles.end()) ... else do with *itr', the else condition.  
The 'itr' is 'end()' now, so we can't handle on the reference of it.  
  
I think they should be:  
  
```  
        foreach_used_handle(exec,  
                [&](::boost::winapi::HANDLE_ handle)  
                {  
                    auto itr = std::find(all_handles.begin(), all_handles .end(), handle);  
                    DWORD flags = 0u;  
                    if (itr != all_handles.end())  
                        *itr = ::boost::winapi::INVALID_HANDLE_VALUE_;  
                });  
        for (auto& h : all_handles) {  
            DWORD flags = 0u;  
            if ((h != ::boost::winapi::INVALID_HANDLE_VALUE_)  
                && (::boost::winapi::GetHandleInformation(h, &flags) != 0)  
                && ((flags & ::boost::winapi::HANDLE_FLAG_INHERIT_) == 0)) //it is NOT inherited anyhow, so ignore too  
                h = ::boost::winapi::INVALID_HANDLE_VALUE_;  
        }  
  
```

---

## Comment 1

> Username: MikkiWells  
> Created at: 2023-06-16 10:24:51 UTC  
> Url: https://github.com/boostorg/process/issues/284#issuecomment-1594461950  

I'd love to see a fix for this as it causes a crash if handle isn't contained in all_handles.  
Because of this crash, we cannot use limit_handles_ on Windows.
