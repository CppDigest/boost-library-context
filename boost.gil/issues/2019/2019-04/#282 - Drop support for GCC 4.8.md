# #282 - Drop support for GCC 4.8? [Closed]

> Username: mloskot  
> Created at: 2019-04-15 17:01:49 UTC  
> Updated at: 2020-04-17 17:26:57 UTC  
> Closed at: 2019-04-28 13:50:57 UTC  
> Url: https://github.com/boostorg/gil/issues/282  

### UPDATE  
  
GCC 4.8 is ending its life and we should drop support for it in GIL (in Boost 1.71). The conclusion is based on the fact MP11 does not fully support 4.8, see https://github.com/boostorg/gil/issues/282#issuecomment-486871878  
  
Decision announced at https://lists.boost.org/boost-gil/2019/04/0185.php and implemented in #296  
  
------  
  
### Problem  
  
Addition of the Boost.Config feature checks in #260, possibly also `cxxstd=11` in #266, revealed that GIL is practically not compilable with GCC 4.7, 4.8 and 4.9:  
  
Despite https://en.cppreference.com/w/cpp/compiler_support says:  
  
constexpr | N2235 | c++11 | 4.6  
-- | -- | -- | --  
  
I asked around about what is usable version of GCC for C++11 support and the answer was: 4.8 core, 4.9 library, 5 without bugs.  
  
### GCC 4.7  
  
This is also being confirmed with one of the latest [regression builds](https://www.boost.org/development/tests/develop/developer/gil.html) by [teeks99-02-dg4.7-Docker-64on64](https://www.boost.org/development/tests/develop/teeks99-02-dg4-7-Docker-64on64.html) with `gcc-4.7~c++11`.  
  
### GCC 4.9  
  
The related build job is failing on CircleCI: https://circleci.com/workflow-run/92e6ec35-ac6a-428a-aec7-ec65ec340ed5  
  
```  
$ g++-4.9 test.cpp  
test.cpp: In function ‘int main()’:  
test.cpp:3:11: error: expected nested-name-specifier before ‘X’  
     using X = int;  
           ^  
```  
  
### Proposal  
  
1. Remove all CI jobs using GCC 4.x. which are actually failing now (see [CircleCI latest build](  
2. Ensure GCC 5 is CI-tested  
3. List GCC 5.x  as minimal version supported in README  
  
Comments? Announced also on the list https://lists.boost.org/boost-gil/2019/04/0182.php  
/cc @stefanseefeld, @chhenning

---

## Comment 1

> Username: mloskot  
> Created at: 2019-04-15 22:49:59 UTC  
> Url: https://github.com/boostorg/gil/issues/282#issuecomment-483447218  

The UB-ifying specialisations are gone as per #283 but there are uses of `std::is_trivially_default_constructible`  
  
https://github.com/boostorg/gil/blob/6a5772144b5dd030045acc1bb7d92d73e1bb43c3/include/boost/gil/algorithm.hpp#L665-L673  
  
which are failing to compile with GCC 4.7-4.9, https://circleci.com/workflow-run/94304443-646d-4256-b0eb-05c07eda8f65  
  
Those are two encountered so far, but there may be more among the C++11 traits which are missing from the standard library deployed for GCC 4.7-4.9 .

---

## Comment 2

> Username: mloskot  
> Created at: 2019-04-18 15:32:25 UTC  
> Updated at: 2019-04-18 15:32:47 UTC  
> Url: https://github.com/boostorg/gil/issues/282#issuecomment-484563157  

The #291 fixed this issue only partially, for GCC 4.9 only. The build using GCC 4.8 is still failing.

---

## Comment 3

> Username: mloskot  
> Created at: 2019-04-25 23:00:31 UTC  
> Updated at: 2019-04-26 09:40:04 UTC  
> Url: https://github.com/boostorg/gil/issues/282#issuecomment-486867525  

### GCC 4.8  
  
Remaining failures is CircleCI workflow https://circleci.com/workflow-run/000865d8-f3d6-4908-b634-87931c4ad62b where the GCC 4.8 job is failing: https://circleci.com/gh/boostorg/gil/8650  
  
```  
libs/gil/test/pixel/packed_pixel.cpp:79:5: error: static assertion failed: 1st element of packed_channel_references_vector should be packed_channel_reference of 1st channel  
     static_assert(std::is_same  
     ^  
```  
  
for this bit  
  
https://github.com/boostorg/gil/blob/3747846acbd141c503efaa835728487110f6fc6f/test/pixel/packed_pixel.cpp#L79-L84  
  
I'm suspicious about GCC 4.8 and its `libstdc++`. Unfortunately, I'm not able to verify what is libstdc++used on Circle CI (images from https://hub.docker.com/_/gcc/).  
  
### Minimal Example   
  
I have managed to reproduce the issue with GCC 4.8 with MWE https://godbolt.org/z/eCsakP and asked about it on https://lists.boost.org/Archives/boost/2019/04/246075.php

---

## Comment 4

> Username: mloskot  
> Created at: 2019-04-25 23:21:41 UTC  
> Url: https://github.com/boostorg/gil/issues/282#issuecomment-486871878  

As @pdimov helpfully pointed out, there is a GCC 4.8 bug in preserving const, and MP11 includes some workarounds in its own tests https://github.com/boostorg/mp11/blob/e5d6e0d0b991a99c71a2b8f5057d0f0a1a89e060/test/mp_quote_trait.cpp#L47-L54  
  
```  
#if !BOOST_MP11_WORKAROUND( BOOST_MP11_GCC, < 40900 )  
  
        // g++ 4.7, 4.8 have difficulties with preserving top-level const  
  
        BOOST_TEST_TRAIT_TRUE((std::is_same<mp_invoke_q<Q, void>, void const>));  
        BOOST_TEST_TRAIT_TRUE((std::is_same<mp_invoke_q<Q, int[]>, int const[]>));  
  
#endif  
```  
  
Since MP11 originally only supported GCC 4.9 or later and GCC 4.8 is dying (a week left), I propose to we drop support for GCC 4.8.  
  
@stefanseefeld thoughts?
