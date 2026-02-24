# #118 - MinGW compilation errors after porting to Boost.WinAPI because of missing casts [Closed]

> Username: Lastique  
> Created at: 2020-06-07 11:50:12 UTC  
> Updated at: 2020-06-11 20:11:34 UTC  
> Closed at: 2020-06-11 20:11:34 UTC  
> Url: https://github.com/boostorg/interprocess/issues/118  

[Here](https://ci.appveyor.com/project/Lastique/log/builds/33362126/job/tm5v29t53325xsxc#L751) is an example of a failure:  
  
```  
In file included from ./boost/interprocess/permissions.hpp:30:0,  
                 from libs\log\src\permissions.cpp:18:  
./boost/interprocess/detail/win32_api.hpp: In constructor 'boost::interprocess::winapi::interprocess_all_access_security::interprocess_all_access_security()':  
./boost/interprocess/detail/win32_api.hpp:719:73: error: cannot convert 'boost::interprocess::winapi::interprocess_security_descriptor* {aka boost::winapi::_SECURITY_DESCRIPTOR*}' to 'PSECURITY_DESCRIPTOR {aka _SECURITY_DESCRIPTOR*}' for argument '1' to 'BOOL InitializeSecurityDescriptor(PSECURITY_DESCRIPTOR, DWORD)'  
       if(!InitializeSecurityDescriptor(&sd, security_descriptor_revision))  
                                                                         ^  
./boost/interprocess/detail/win32_api.hpp:721:58: error: cannot convert 'boost::interprocess::winapi::interprocess_security_descriptor* {aka boost::winapi::_SECURITY_DESCRIPTOR*}' to 'PSECURITY_DESCRIPTOR {aka _SECURITY_DESCRIPTOR*}' for argument '1' to 'BOOL SetSecurityDescriptorDacl(PSECURITY_DESCRIPTOR, BOOL, PACL, BOOL)'  
       if(!::SetSecurityDescriptorDacl(&sd, true, 0, false))  
                                                          ^  
```  
  
The problem is because `boost::winapi::_SECURITY_DESCRIPTOR` and `::_SECURITY_DESCRIPTOR` are two distinct types, although binary compatible. Boost.WinAPI provides wrapper functions that perform a cast for all such cases, but Boost.Interprocess is not using them and is not doing a cast either. The fix is to use Boost.WinAPI wrappers (e.g. `boost::winapi::InitializeSecurityDescriptor`).

---

## Comment 1

> Username: Lastique  
> Created at: 2020-06-07 16:05:29 UTC  
> Url: https://github.com/boostorg/interprocess/issues/118#issuecomment-640240983  

After looking into this more closely, it turned out to be caused by a different definition of `::PSECURITY_DESCRIPTOR` on MinGW - as a pointer to `::_SECURITY_DESCRIPTOR`. On other Windows SDKs it is defined as `PVOID`, which is why the problem did not occur anywhere else (the cast to `void*` is performed implicitly).  
  
In any case, I've added a workaround in Boost.WinAPI and in https://github.com/boostorg/interprocess/pull/119 switched to Boost.WinAPI wrapper functions, which should solve the problem.
