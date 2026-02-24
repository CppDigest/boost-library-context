# #73 Merging master in develop [Merged]

> Username: daminetreg  
> Created at: 2015-04-29 14:39:04 UTC  
> Updated at: 2015-04-29 14:54:15 UTC  
> Merged at: 2015-04-29 14:54:15 UTC  
> Closed at: 2015-04-29 14:54:15 UTC  
> Url: https://github.com/boostorg/fusion/pull/73  

I forgot to target develop first when doing the changes, sorry for this. Here are the changes of master merged back to develop with all tests passing for :   
- bjam toolset=gcc -j8 -a cxxflags="--std=c++98 -DBOOST_PP_VARIADICS=0"  
- bjam toolset=gcc -j8 -a cxxflags="--std=c++98 -DBOOST_PP_VARIADICS=1"  
- bjam toolset=gcc -j8 -a cxxflags="--std=c++11 -DBOOST_PP_VARIADICS=0"  
- bjam toolset=gcc -j8 -a cxxflags="--std=c++11 -DBOOST_PP_VARIADICS=1"  
- bjam toolset=gcc -j8 -a cxxflags="--std=c++14 -DBOOST_PP_VARIADICS=0"  
- bjam toolset=gcc -j8 -a cxxflags="--std=c++14 -DBOOST_PP_VARIADICS=1"  
- bjam toolset=clang -j8 -a cxxflags="--std=c++98 -DBOOST_PP_VARIADICS=0"  
- bjam toolset=clang -j8 -a cxxflags="--std=c++98 -DBOOST_PP_VARIADICS=1"  
- bjam toolset=clang -j8 -a cxxflags="--std=c++11 -DBOOST_PP_VARIADICS=0"  
- bjam toolset=clang -j8 -a cxxflags="--std=c++11 -DBOOST_PP_VARIADICS=1"  
- bjam toolset=clang -j8 -a cxxflags="--std=c++14 -DBOOST_PP_VARIADICS=0"  
- bjam toolset=clang -j8 -a cxxflags="--std=c++14 -DBOOST_PP_VARIADICS=1"  
  
Cheers,

---
