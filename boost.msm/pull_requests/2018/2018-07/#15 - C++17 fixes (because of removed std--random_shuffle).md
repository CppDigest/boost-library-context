# #15 C++17 fixes (because of removed std::random_shuffle) [Merged]

> Username: DenizThatMenace  
> Created at: 2018-07-11 10:00:54 UTC  
> Updated at: 2018-08-24 15:43:52 UTC  
> Merged at: 2018-08-24 15:43:52 UTC  
> Closed at: 2018-08-24 15:43:52 UTC  
> Url: https://github.com/boostorg/msm/pull/15  

With C++17 `std::random_shuffle` was removed and because of that compilation fails with errors like this:  
  
  
```  
In file included from BoostMsmCompilerStressTest.cpp:24:  
In file included from .../boost/msm/front/euml/stl.hpp:15:  
In file included from .../boost/msm/front/euml/algorithm.hpp:16:  
.../boost/msm/front/euml/transformation.hpp:42:47: error: no member named 'random_shuffle' in namespace 'std'  
BOOST_MSM_EUML_FUNCTION(RandomShuffle_ , std::random_shuffle , random_shuffle_ , void , void )  
                                         ~~~~~^  
.../boost/msm/front/euml/common.hpp:2185:16: note: expanded from macro 'BOOST_MSM_EUML_FUNCTION'  
        return function (Param1()(evt,fsm,src,tgt));}                                                   \  
               ^~~~~~~~  
```  
  
This pull-request fixes it by conditionally removing that action `RandomShuffle_` if compiled with C++17 or later.  
  
Additionally, it conditionally adds a new action `Shuffle_` if compiled with C++11 or later.

---
