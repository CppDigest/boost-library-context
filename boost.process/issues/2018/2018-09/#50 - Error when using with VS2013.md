# #50 - Error when using with VS2013 [Closed]

> Username: ghost  
> Created at: 2018-09-13 08:45:22 UTC  
> Updated at: 2018-09-14 01:26:45 UTC  
> Closed at: 2018-09-14 01:26:45 UTC  
> Url: https://github.com/boostorg/process/issues/50  

I have compiled boost 1.68 with VS2013 and when I try to use it I have the following errors:  
  
1) in include\boost\process\detail\config.hpp L64 there is the c++ noexcept keyword but VS2013 do not support it. Why it doesn't use the BOOST_NOEXCEPT define instead ?  
  
2) include\boost/process/detail/config.hpp(72): error C2660: 'boost::process::process_error::process_error' : function does not take 2 arguments

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-09-14 01:26:43 UTC  
> Url: https://github.com/boostorg/process/issues/50#issuecomment-421201608  

Because it's a C++11 library, there are other design issues that can't be worked around.
