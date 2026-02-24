# #71 gcc.jam mips1 fix [Merged]

> Username: ClaymorePT  
> Created at: 2015-04-24 01:55:15 UTC  
> Updated at: 2021-10-02 22:20:29 UTC  
> Merged at: 2015-04-24 15:24:59 UTC  
> Closed at: 2015-04-24 15:24:59 UTC  
> Url: https://github.com/boostorg/build/pull/71  

gcc.jam was passing the options -m32 and -m64 to mips cross-compilers, when those do not use such options  
  This modification solves it by adding mips1 as an exception.  
  
This issue was causing problems when building MIPS based targets for OpenWRT.  
The problem is discussed here: https://github.com/openwrt/packages/issues/1160  
  
Carlos

---

## Comment 1

> Username: joerg-krause  
> Created_at: 2015-05-02 11:32:24 UTC  
> Url: https://github.com/boostorg/build/pull/71#issuecomment-98349663  

Boost 1.58 does not build for SuperH based targets with [Buildroot](http://autobuild.buildroot.net/results/ccd/ccd5c83963032ba49b1627b1dff39e34a9486943/build-end.log), too:  
  
Rather then excluding all architectures not supporting `-m32` and `-m64` including all architectures would help here.

---

## Comment 2

> Username: ClaymorePT  
> Created_at: 2015-05-03 18:04:05 UTC  
> Url: https://github.com/boostorg/build/pull/71#issuecomment-98518079  

That is true. Unfortunately this is a problem upstream.  
  
The best I could do is to create a better patch for all available archs in  
OpenWRT.  
I need to cross-check which archs is Boost currently supporting and see if  
there is any incompatibility.  
  
On 2 May 2015 at 12:32, Jörg Krause notifications@github.com wrote:  
  
> Boost 1.58 does not build for SuperH based targets with Buildroot  
> http://autobuild.buildroot.net/results/ccd/ccd5c83963032ba49b1627b1dff39e34a9486943/build-end.log,  
> too:  
>   
> Rather then excluding all architectures not supporting -m32 and -m64  
> including all architectures would help here.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/build/pull/71#issuecomment-98349663.  
  
##   
  
Carlos Miguel Ferreira  
Researcher at Telecommunications Institute  
Aveiro - Portugal  
Work E-mail - cmf@av.it.pt  
Skype & GTalk -> carlosmf.pt@gmail.com  
LinkedIn -> http://www.linkedin.com/in/carlosmferreira

---

## Comment 3

> Username: vprus  
> Created_at: 2015-05-03 18:56:53 UTC  
> Url: https://github.com/boostorg/build/pull/71#issuecomment-98523456  

Maybe we can't do much better - we now always set address-model property, so we'd have to issue an error if address-model property is different from the toolset default, and for that we'd need to record the toolset default, and we don't have a way to do this right now. I'm leaning towards applying your last commit, but I'll think a bit more about it.

---

## Comment 4

> Username: joerg-krause  
> Created_at: 2015-05-03 21:01:59 UTC  
> Url: https://github.com/boostorg/build/pull/71#issuecomment-98544002  

I've prepared a patch addressing this issue: https://github.com/boostorg/build/pull/76  
  
We had also a small discussion at Buildroot:  https://patchwork.ozlabs.org/patch/466950/

---

## Comment 5

> Username: ClaymorePT  
> Created_at: 2015-05-03 23:25:57 UTC  
> Url: https://github.com/boostorg/build/pull/71#issuecomment-98555950  

I will also update the patch for OpenWRT according to joerg-krause update.   
Thanks for the update joerg.

---
