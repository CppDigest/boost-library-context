# #559 - Boost library linking error [Closed]

> Username: lalishansh  
> Created at: 2021-09-11 13:24:07 UTC  
> Updated at: 2021-09-11 14:23:18 UTC  
> Closed at: 2021-09-11 14:23:18 UTC  
> Url: https://github.com/boostorg/boost/issues/559  

LINK : fatal error LNK1104: cannot open file 'libboost_thread-vc142-mt-gd-x32-1_77.lib'  
  
Using Visual Studio 2019 16.11.2  
  
I have build libraries correctly the problem is b2 build tool is building version v1.78 instead of v1.77 (released version is actually required by the linker).  
  
so how do i build v1.77 instead of v1.78 or how do tell linker to use v1.78 instead of  v1.77

---

## Comment 1

> Username: mclow  
> Created at: 2021-09-11 13:31:25 UTC  
> Url: https://github.com/boostorg/boost/issues/559#issuecomment-917408225  

You start with the 1.77.0 sources, not the current set from GitHub.  Download them from https://boostorg.jfrog.io/ui/native/main/release/1.77.0/source/

---

## Comment 2

> Username: lalishansh  
> Created at: 2021-09-11 13:37:15 UTC  
> Url: https://github.com/boostorg/boost/issues/559#issuecomment-917409051  

😐 well is there really not a better way

---

## Comment 3

> Username: lalishansh  
> Created at: 2021-09-11 13:38:24 UTC  
> Updated at: 2021-09-11 13:42:11 UTC  
> Url: https://github.com/boostorg/boost/issues/559#issuecomment-917409211  

than downloading everytime i want to update boost version for my project

---

## Comment 4

> Username: mclow  
> Created at: 2021-09-11 13:43:50 UTC  
> Url: https://github.com/boostorg/boost/issues/559#issuecomment-917410007  

Sure. Fix your build system to link with whatever version of boost you're using. It changes about every four months.

---

## Comment 5

> Username: lalishansh  
> Created at: 2021-09-11 13:47:21 UTC  
> Url: https://github.com/boostorg/boost/issues/559#issuecomment-917410549  

with BOOST_VERSION ?

---

## Comment 6

> Username: lalishansh  
> Created at: 2021-09-11 14:19:46 UTC  
> Updated at: 2021-09-11 14:20:16 UTC  
> Url: https://github.com/boostorg/boost/issues/559#issuecomment-917415372  

yup with   
#define BOOST_VERSION 107800   
&  
#define BOOST_LIB_VERSION "1_78"  
it works fine
