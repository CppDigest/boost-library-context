# #75 - Latest beta (1.89 beta 1) fails to compile with MSVC [Closed]

> Username: wyattoday  
> Created at: 2025-07-16 13:38:02 UTC  
> Updated at: 2025-07-16 14:41:39 UTC  
> Closed at: 2025-07-16 14:41:39 UTC  
> Url: https://github.com/boostorg/atomic/issues/75  

Download latest boost beta, open a MSVC x64 native tools prompt (to set all the environmental variables correctly), run the following commands:  
  
```  
cd c:\path\to\beta1.89  
bootstrap.bat  
```  
  
Then compile with a pretty vanilla build statement:  
  
```  
b2 toolset=msvc-14.3 address-model=64 variant=release threading=multi link=static runtime-link=shared,static cxxflags="/O2 /Ob2 /Oy /Oi /Os /DWINVER=0x0600 /D_WIN32_WINNT=0x0600 /D_WIN32_WINDOWS=0x0600 /std:c++20" define=BOOST_USE_WINAPI_VERSION=0x0600 --with-chrono --with-thread --stagedir=libs-windows\vs2022\x64 stage -j32  
```  
  
Get the following errors at the bottom:  
  
```  
...failed updating 2 targets...  
   compile-c-c++ bin.v2\libs\thread\build\msvc-14.3\release\x86_64\link-static\runtime-link-static\threadapi-win32\threading-multi\win32\thread_primitives.obj  
   compile-c-c++ bin.v2\libs\thread\build\msvc-14.3\release\x86_64\link-static\threadapi-win32\threading-multi\win32\thread_primitives.obj  
```  
  
  
Caused by the following bug(s) in boost.atomic (scroll up in the compilation log):  
  
```  
compile-c-c++ bin.v2\libs\thread\build\msvc-14.3\release\x86_64\link-static\runtime-link-static\threadapi-win32\threading-multi\win32\thread_primitives.obj  
thread_primitives.cpp  
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
```

---

## Comment 1

> Username: Lastique  
> Created at: 2025-07-16 14:41:39 UTC  
> Url: https://github.com/boostorg/atomic/issues/75#issuecomment-3078919334  

Boost.Atomic now requires Windows 10 or later. You need to update your command line to define `_WIN32_WINNT=0x0A00` and the other macros similarly.
