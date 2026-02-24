# #332 - Boost\libs\fiber\test failed to build due to error C2039 with /std:c++latest option on Windows [Open]

> Username: QuellaZhang  
> Created at: 2025-11-14 08:25:28 UTC  
> Updated at: 2025-11-20 08:46:59 UTC  
> Url: https://github.com/boostorg/fiber/issues/332  

### Operating System  
Windows 11  
Windows SDK 10.0.26100.0  
  
### Describe the issue  
The MSVC team regularly builds popular open-source projects, including yours, with development versions of the build tools in order to find and fix regressions in the compiler and libraries before they can ship and cause trouble for the world. This also allows us to provide advance notice of breaking changes, which is the case here.  
  
fiber failed to build due to error C2039 with /std:c++latest option on Windows. Could you please help to take a look at this? Thanks in advance!  
  
### Steps to reproduce  
  
1. Open VS2022 x64 CMD.  
2. git clone --recurse https://github.com/boostorg/boost.git C:\gitP\boostorg\boost  
3. cd /d C:\gitP\boostorg\boost  
4. `set _CL_=/std:c++latest`  
5. .\bootstrap  
6. .\b2 headers variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86  
7. .\b2 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 address-model=64 architecture=x86  
8. .\b2 -j16 variant=release --build-dir=C:\gitP\boostorg\boost\amd64 libs\fiber\test address-model=64  
  
### Test.log:   
  
[Test.log](https://github.com/user-attachments/files/23542834/Test.log)  
  
### Error info:  
```  
.\boost/atomic/detail/wait_ops_windows.hpp(66): error C2039: 'WaitOnAddress': is not a member of 'boost::winapi'  
.\boost/winapi/wait_constants.hpp(19): note: see declaration of 'boost::winapi'  
.\boost/atomic/detail/wait_ops_windows.hpp(66): note: the template instantiation context (the oldest one first) is  
.\boost/atomic/detail/wait_ops_windows.hpp(48): note: while compiling class template 'boost::atomics::detail::wait_operations_windows'  
.\boost/atomic/detail/wait_ops_windows.hpp(95): error C2039: 'WaitOnAddress': is not a member of 'boost::winapi'  
.\boost/winapi/wait_constants.hpp(19): note: see declaration of 'boost::winapi'  
.\boost/atomic/detail/wait_ops_windows.hpp(75): note: This diagnostic occurred in the compiler generated function 'wait_operations_windows<Base,Size>::base_type::storage_type boost::atomics::detail::wait_operations_windows<Base,Size>::wait_until_impl(volatile const wait_operations_windows<Base,Size>::base_type::storage_type &,wait_operations_windows<Base,Size>::base_type::storage_type,Clock::time_point,Clock::time_point,boost::memory_order,bool &) noexcept(<expr>)'  
.\boost/atomic/detail/wait_ops_windows.hpp(141): error C2039: 'WakeByAddressSingle': is not a member of 'boost::winapi'  
.\boost/winapi/wait_constants.hpp(19): note: see declaration of 'boost::winapi'  
.\boost/atomic/detail/wait_ops_windows.hpp(146): error C2039: 'WakeByAddressAll': is not a member of 'boost::winapi'  
.\boost/winapi/wait_constants.hpp(19): note: see declaration of 'boost::winapi'  
.\boost/bind.hpp(41): note: The practice of declaring the Bind placeholders (_1, _2, ...) in the global namespace is deprecated. Please use <boost/bind/bind.hpp> + using namespace boost::placeholders, or define BOOST_BIND_GLOBAL_PLACEHOLDERS to retain the current behavior.  
```

---

## Comment 1

> Username: QuellaZhang  
> Created at: 2025-11-20 08:46:59 UTC  
> Url: https://github.com/boostorg/fiber/issues/332#issuecomment-3556634125  

Changing `0x0601 `to `0x0601 `will solve this problem.  
` -    <target-os>windows:<define>_WIN32_WINNT=0x0601`  
` +    <target-os>windows:<define>_WIN32_WINNT=0x0602`
