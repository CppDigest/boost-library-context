# #70 - compile break in 1.85.0.beta1 when using favor_compile_time [Closed]

> Username: ChristophStrehle  
> Created at: 2024-03-21 08:30:08 UTC  
> Updated at: 2024-03-24 17:57:22 UTC  
> Closed at: 2024-03-24 17:57:22 UTC  
> Url: https://github.com/boostorg/msm/issues/70  

When instantiating the boost::msm::back::state_machine template with the boost::msm::back::favor_compile_time back end I get the following compile break:  
  
`boost\msm\back\metafunctions.hpp(651,50): error C2039: 'as_vector': is not a member of 'boost::fusion::result_of'`

---

## Comment 1

> Username: henry-ch  
> Created at: 2024-03-24 17:57:22 UTC  
> Url: https://github.com/boostorg/msm/issues/70#issuecomment-2016886687  

fixed in develop. Will request merge into 1.85 master.
