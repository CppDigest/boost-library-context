# #48 - Compile break in 1.84.0.b1.rc1 when using favor_compile_time back end [Closed]

> Username: ChristophStrehle  
> Created at: 2023-11-13 06:52:48 UTC  
> Updated at: 2024-02-08 15:53:47 UTC  
> Closed at: 2024-02-08 15:53:47 UTC  
> Url: https://github.com/boostorg/msm/issues/48  

When instantiating the `boost::msm::back::state_machine` template with the `boost::msm::back::favor_compile_time` back end I get the following compile break:  
  
`boost\msm\back\state_machine.hpp(2006,24): error C2064: term does not evaluate to a function taking 0 arguments`  
  
This happens since this commit:  
https://github.com/boostorg/msm/commit/72d98ac46d5ac8a9fe9ea70b2e39870764ea85c6  
  
I guess the instance variable in favor_compile_time.hpp should be a function as well  
https://github.com/boostorg/msm/blob/develop/include/boost/msm/back/favor_compile_time.hpp#L307
