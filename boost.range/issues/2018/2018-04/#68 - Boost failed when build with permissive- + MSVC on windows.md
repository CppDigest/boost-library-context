# #68 - Boost failed when build with permissive- + MSVC on windows. [Open]

> Username: QuellaZhang  
> Created at: 2018-04-23 10:00:49 UTC  
> Updated at: 2018-04-26 14:56:48 UTC  
> Url: https://github.com/boostorg/range/issues/68  

Boost failed with error C3646, C4430, C3861 when build with permissive- on Windows, I use latest source on master branch. Could you please help take a look at this? Noted that this issue only found when compiles with unreleased vctoolset, that next release of MSVC will have this behavior.  
   
**You can repro this issue as the steps below:**  
  
1. git clone -c core.autocrlf=true --recursive https://github.com/boostorg/boost.git D:\Boost\src  
2. Open a VS 2015 x86 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\Release --address-model=32  
5. .\b2 variant=release --build-dir=..\out\Release --address-model=32  
  
**Error info:**  
.\boost/range/concepts.hpp(257): error C3646: 'n': unknown override specifier  
.\boost/range/concepts.hpp(264): note: see reference to class template instantiation 'boost::range_detail::RandomAccessIteratorConcept<Iterator>' being compiled  
.\boost/range/concepts.hpp(257): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
.\boost/range/concepts.hpp(247): error C3861: 'n': identifier not found  
.\boost/range/concepts.hpp(248): error C3861: 'n': identifier not found  
.\boost/range/concepts.hpp(249): error C3861: 'n': identifier not found  
.\boost/range/concepts.hpp(250): error C3861: 'n': identifier not found  
.\boost/range/concepts.hpp(251): error C3861: 'n': identifier not found  
.\boost/range/concepts.hpp(252): error C3861: 'n': identifier not found

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-04-25 22:13:09 UTC  
> Url: https://github.com/boostorg/range/issues/68#issuecomment-384450682  

I'm having this problem with the release version of MSVC.

---

## Comment 2

> Username: DanielaE  
> Created at: 2018-04-26 14:56:48 UTC  
> Url: https://github.com/boostorg/range/issues/68#issuecomment-384671276  

This is a duplicate of issue #66
