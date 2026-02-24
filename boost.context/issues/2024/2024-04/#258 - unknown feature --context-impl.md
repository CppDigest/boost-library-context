# #258 - unknown feature "<context-impl>" [Closed]

> Username: gpascalauteufel  
> Created at: 2024-04-05 15:32:09 UTC  
> Updated at: 2024-04-08 13:54:12 UTC  
> Closed at: 2024-04-08 13:54:12 UTC  
> Url: https://github.com/boostorg/context/issues/258  

Trying to build Boost 1.82 as part of Yocto for an `aarch64` target (ARM 64 bit) and trying to switch to `ucontext` instead of `fcontext` for better sanitizer support.  
  
I'm using the flags:  
  
```sh  
./b2 install context-impl=ucontext define=BOOST_USE_UCONTEXT define=BOOST_USE_ASAN  
```  
  
However, I'm getting:  
  
```sh  
warning: Configuring default toolset "gcc".  
1_1.82.0-r0/git/src/build/feature.jam:327: in validate-feature from module feature  
error: unknown feature "<context-impl>"  
```  
  
I'm having no problem building the same version of Boost for x86. What am I missing? Please let me know what other type of logs/info is needed. Thanks in advance.  
  
LE: apologies, for closing and re-opening the ticket. Was a misclick :S

---

## Comment 1

> Username: gpascalauteufel  
> Created at: 2024-04-08 13:54:12 UTC  
> Url: https://github.com/boostorg/context/issues/258#issuecomment-2042823357  

Yocto misconfiguration, not relevant to Boost Context.
