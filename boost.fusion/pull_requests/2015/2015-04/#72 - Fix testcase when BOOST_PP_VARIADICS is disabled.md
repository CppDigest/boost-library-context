# #72 Fix testcase when BOOST_PP_VARIADICS is disabled [Merged]

> Username: daminetreg  
> Created at: 2015-04-29 14:13:08 UTC  
> Updated at: 2015-04-29 14:14:33 UTC  
> Merged at: 2015-04-29 14:14:33 UTC  
> Closed at: 2015-04-29 14:14:33 UTC  
> Url: https://github.com/boostorg/fusion/pull/72  

As discovered by @Flast one testcase had a comma too much when BOOST_PP_VARIADICS is disabled. This commit just remove this comma.  
  
Tested with gcc-4.9.2 :   
- bjam -j8 -a cxxflags="--std=c++98 -DBOOST_PP_VARIADICS=0"  
- bjam -j8 -a cxxflags="--std=c++98 -DBOOST_PP_VARIADICS=1"  
- bjam -j8 -a cxxflags="--std=c++11 -DBOOST_PP_VARIADICS=0"  
- bjam -j8 -a cxxflags="--std=c++11 -DBOOST_PP_VARIADICS=1"  
- bjam -j8 -a cxxflags="--std=c++14 -DBOOST_PP_VARIADICS=0"  
- bjam -j8 -a cxxflags="--std=c++14 -DBOOST_PP_VARIADICS=1"  
  
So this time should be ok. :smile:

---
