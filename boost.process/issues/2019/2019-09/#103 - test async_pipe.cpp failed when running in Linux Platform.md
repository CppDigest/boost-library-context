# #103 - test async_pipe.cpp failed when running in Linux Platform [Closed]

> Username: yuxianch  
> Created at: 2019-09-29 06:17:20 UTC  
> Updated at: 2019-11-22 05:05:22 UTC  
> Closed at: 2019-11-22 05:05:22 UTC  
> Url: https://github.com/boostorg/process/issues/103  

Hi all,  
I got some errors when running test async_pipe.cpp in Linux platform.  
In the 99th and 100th line of async_pipe.cpp file, "::boost::winapi::INVALID_HANDLE_VALUE_" is used, which is defined in Boost.WinAPI.     
If <boost/winapi/handles.hpp> is not included in source code, I got this:  
```  
async_pipe.cpp:99:52: error: no member named 'winapi' in namespace 'boost'  
    BOOST_CHECK_EQUAL(ap.native_source(), ::boost::winapi::INVALID_HANDLE_VALUE_);  
                                          ~~~~~~~~~^  
--  
async_pipe.cpp:100:52: error: no member named 'winapi' in namespace 'boost'  
    BOOST_CHECK_EQUAL(ap.native_sink  (), ::boost::winapi::INVALID_HANDLE_VALUE_);  
                                          ~~~~~~~~~^  
```  
After adding this header, I got:  
```  
In file included from async_pipe.cpp:21:  
In file included from ../../../boost/winapi/handles.hpp:12:  
../../../boost/winapi/basic_types.hpp:37:3: error: "Win32 functions not available"  
# error "Win32 functions not available"  
  ^  
```  
Should this test be disabled in Linux? Or some changes should be made to fix this issue?  
Thanks!
