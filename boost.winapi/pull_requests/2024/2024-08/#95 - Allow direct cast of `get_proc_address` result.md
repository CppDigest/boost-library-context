# #95 Allow direct cast of `get_proc_address` result [Merged]

> Username: Flamefire  
> Created at: 2024-08-25 17:29:58 UTC  
> Updated at: 2024-08-26 11:02:22 UTC  
> Merged at: 2024-08-26 10:57:23 UTC  
> Closed at: 2024-08-26 10:57:23 UTC  
> Url: https://github.com/boostorg/winapi/pull/95  

> GetProcAddress returns a pointer to some function. It can return pointers to different functions, so it has to return something that is suitable to store any pointer to function. Microsoft chose FARPROC, which is int (WINAPI *)() on 32-bit Windows. The user is supposed to know the signature of the function he requests and perform a cast (which is a nop on this platform). The result is a pointer to function with the required signature, which is bitwise equal to what GetProcAddress returned.  
However, gcc >= 8 warns about that.  
  
(See https://github.com/boostorg/thread/pull/405)  
  
  
Add a template parameter to do the cast inside the function and suppress the warning there.  
  
This allows to do the following replacement at the usage site without breaking backwards compatibility:  
  
```diff  
-        GetFileInformationByHandleEx_t* get_file_information_by_handle_ex = (GetFileInformationByHandleEx_t*)get_proc_address(h, "GetFileInformationByHandleEx");  
+        GetFileInformationByHandleEx_t* get_file_information_by_handle_ex = get_proc_address<GetFileInformationByHandleEx_t*>(h, "GetFileInformationByHandleEx");  
```

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-08-25 19:03:08 UTC  
> Url: https://github.com/boostorg/winapi/pull/95#issuecomment-2308959412  

I don't think all libraries that depend on Boost.WinAPI have switched to C++11 or later, so let's keep Boost.WinAPI compatible with C++03 for now.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2024-08-26 10:35:12 UTC  
> Url: https://github.com/boostorg/winapi/pull/95#issuecomment-2309891850  

Oh you are right, default method template params are C++11, been coding to long in at least C++11 by now. Thanks!  
  
Fixed by adding the templated method and calling the non-templated one from there and doing only the cast.

---
