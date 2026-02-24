# #673 - error C2953 when building source that includes boost/gil.hpp and boost/process.hpp [Closed]

> Username: mvfpoa  
> Created at: 2022-07-28 03:37:13 UTC  
> Updated at: 2022-07-28 21:39:51 UTC  
> Closed at: 2022-07-28 21:39:30 UTC  
> Url: https://github.com/boostorg/boost/issues/673  

Windows 10  
Visual Studio 2019  
Boost Version 1.76.0  
  
Create a simple source like this:  
```  
#include <boost/gil.hpp>  
#include <boost/process.hpp>  
```  
  
invoke  
  
`cl.exe simple.cpp /I c:\boost\`  
  
and you are going to get the following error message:  
  
```  
.\boost/fusion/adapted/std_tuple/tag_of.hpp(38): error C2953: 'boost::mpl::sequence_tag<std::tuple<T...>>': class template already defined  
.\boost/mp11/mpl_tuple.hpp(21): note: check the declaration of 'boost::mpl::sequence_tag<std::tuple<T...>>'  
```  
  
I translated the error message to English

---

## Comment 1

> Username: mvfpoa  
> Created at: 2022-07-28 03:39:57 UTC  
> Url: https://github.com/boostorg/boost/issues/673#issuecomment-1197618581  

possible correlation with https://github.com/boostorg/mp11/issues/39

---

## Comment 2

> Username: mclow  
> Created at: 2022-07-28 03:49:23 UTC  
> Url: https://github.com/boostorg/boost/issues/673#issuecomment-1197623390  

I just tried to reproduce this with boost trunk and failed. (apple clang, and Mac OS)  
  
`clang++ -std=c++14 -I $BOOST bug.cpp` compiles w/o error.

---

## Comment 3

> Username: mclow  
> Created at: 2022-07-28 04:00:21 UTC  
> Url: https://github.com/boostorg/boost/issues/673#issuecomment-1197629173  

And I *was* able to reproduce it (apple clang, Mac OS) with the 1.79.0 release.  
Please try the 1.80 beta release (available here: https://boostorg.jfrog.io/artifactory/main/beta/1.80.0.beta1/source/) and report back here if you can reproduce it there.

---

## Comment 4

> Username: mvfpoa  
> Created at: 2022-07-28 21:39:30 UTC  
> Updated at: 2022-07-28 21:39:51 UTC  
> Url: https://github.com/boostorg/boost/issues/673#issuecomment-1198659723  

Confirmed the issue is fixed with the 1.80 beta1. Thanks, @mclow!
