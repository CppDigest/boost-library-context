# #29 - Can't seem to use Boost_COMPILER with windows build [Closed]

> Username: mellery451  
> Created at: 2020-01-07 01:49:05 UTC  
> Updated at: 2020-01-24 18:29:38 UTC  
> Closed at: 2020-01-24 18:29:38 UTC  
> Url: https://github.com/boostorg/boost_install/issues/29  

My Use case: I have built boost with VS2017 (vc141 toolset) and I would like to link to it in a VS2019 project (vc142 toolset). I thought setting `Boost_COMPILER` to "vc141" would allow this, but I always seem to hit the second case here in the config files:  
  
```cmake  
if(Boost_COMPILER AND NOT Boost_COMPILER STREQUAL "vc141")  
  _BOOST_SKIPPED("libboost_chrono-vc141-mt-gd-x64-1_70.lib" "vc141, Boost_COMPILER=${Boost_COMPILER}")  
  return()  
endif()  
  
if(BOOST_DETECTED_TOOLSET AND NOT BOOST_DETECTED_TOOLSET STREQUAL "vc141")  
  _BOOST_SKIPPED("libboost_chrono-vc141-mt-gd-x64-1_70.lib" "vc141, detected ${BOOST_DETECTED_TOOLSET}, set Boost_COMPILER to override")  
  return()  
endif()  
```  
...that is, since `BOOST_DETECTED_TOOLSET` is always set to the auto-detected value (vc142 in my case), it hits the second return here and can't proceed to find the vc141 libs that do exist.   
  
Am I doing something wrong or is it possible that the `BOOST_DETECTED_TOOLSET` block above shouldn't be in the generated configs perhaps?

---

## Comment 1

> Username: mellery451  
> Created at: 2020-01-07 17:29:47 UTC  
> Url: https://github.com/boostorg/boost_install/issues/29#issuecomment-571688815  

Another side (possibly unrelated..) note is that the config files seem to expect a `Boost_COMPILER` value of the form `vc141` (with no leading dash), but the classic find module seems to want it to include the leading dash (e.g. `-vc141`). It's not a huge deal, but that is a small inconsistency that might trip-up people (like me). Thanks.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2020-01-24 18:10:09 UTC  
> Url: https://github.com/boostorg/boost_install/issues/29#issuecomment-578239381  

@pdimov  is this a CMake issue?

---

## Comment 3

> Username: pdimov  
> Created at: 2020-01-24 18:18:48 UTC  
> Url: https://github.com/boostorg/boost_install/issues/29#issuecomment-578242559  

It is a CMake issue.

---

## Comment 4

> Username: pdimov  
> Created at: 2020-01-24 18:19:50 UTC  
> Url: https://github.com/boostorg/boost_install/issues/29#issuecomment-578242910  

I believe that this was fixed for either 1.71 or 1.72.

---

## Comment 5

> Username: pdimov  
> Created at: 2020-01-24 18:22:00 UTC  
> Url: https://github.com/boostorg/boost_install/issues/29#issuecomment-578243721  

1.71, with https://github.com/boostorg/boost_install/commit/9967d60ec1a6eaa8a4781f61f87d621a89b605a9 and https://github.com/boostorg/boost_install/commit/f24f8208155614a82e9c0e4bacde5559f5420b26.

---

## Comment 6

> Username: mellery451  
> Created at: 2020-01-24 18:29:38 UTC  
> Url: https://github.com/boostorg/boost_install/issues/29#issuecomment-578246510  

thanks - I'll update.
