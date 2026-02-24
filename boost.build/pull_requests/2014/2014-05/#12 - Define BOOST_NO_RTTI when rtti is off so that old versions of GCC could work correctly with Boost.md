# #12 Define BOOST_NO_RTTI when rtti is off so that old versions of GCC could work correctly with Boost [Closed]

> Username: apolukhin  
> Created at: 2014-05-15 08:34:15 UTC  
> Updated at: 2021-10-02 22:36:02 UTC  
> Closed at: 2014-05-22 13:16:32 UTC  
> Url: https://github.com/boostorg/build/pull/12  



---

## Comment 1

> Username: vprus  
> Created_at: 2014-05-15 09:49:38 UTC  
> Url: https://github.com/boostorg/build/pull/12#issuecomment-43189852  

Can't this be done in boost/config somehow? I did not research it, but if there's no way to check whether rtti is enabled, it seems reasonable to file a gcc enhancement request.  
  
It also seems that adding <rtti>off:<define>BOOST_NO_RTTI to Jamroot would be a more localized solution.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2014-05-15 11:55:28 UTC  
> Url: https://github.com/boostorg/build/pull/12#issuecomment-43200461  

boost/config attempts to detect RTTI, but it has some limitations:  
  
```  
//  
// RTTI and typeinfo detection is possible post gcc-4.3:  
//  
```  
  
GCC-4.2 is used by default on Darvin platform.  
  
> It also seems that adding off:BOOST_NO_RTTI to Jamroot would be a more localized solution.  
  
Working out of the box is what I'd love to see. I'm not very familiar with Boost.Build sources and possibly there is a better way to do the BOOST_NO_RTTI stuff.

---

## Comment 3

> Username: Belcourt  
> Created_at: 2014-05-16 00:07:06 UTC  
> Url: https://github.com/boostorg/build/pull/12#issuecomment-43280417  

I tested this on a couple of older macs, it seems to work fine (the mods to gcc.jam).  I think this is a good, though perhaps non-standard, approach (defining a Boost macro in Jam).  I'd note that gcc-4.2.1 on Mac (non-clang version) actually has working RTTI so just passing -fno-rtti is sufficient to turn off rtti.  Perhaps we should err on the safe side and apply just the patch to gcc.jam but not msvc since that patch is much larger. See how the tests do when they cycle, then separately apply the msvc patch.

---

## Comment 4

> Username: vprus  
> Created_at: 2014-05-16 06:02:56 UTC  
> Url: https://github.com/boostorg/build/pull/12#issuecomment-43299989  

I'm -0.5 on a change that add BOOST_ macros to gcc.jam, or any toolchain. Imagine if everybody would that - we'd have gcc.jam define zillion of macros for the needs of every boost library. I think conditional requirements is quite appropriate mechanism to handle this.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2014-05-19 21:47:43 UTC  
> Url: https://github.com/boostorg/build/pull/12#issuecomment-43562807  

I'm a -1 on this. We should avoid defining project specific macros in toolsets. Not just for the sake of clarity. But it can actually make debugging problems in configurations harder. This can definitely be done with a conditional only in the Boost project jamfiles. The root project file seems like the ideal place to me.

---

## Comment 6

> Username: vprus  
> Created_at: 2014-05-22 12:48:58 UTC  
> Url: https://github.com/boostorg/build/pull/12#issuecomment-43883086  

Antony,  
  
given this feedback, will you be willing to try adding   
  
```  
 <rtti>off:<define>BOOST_NO_RTTI  
```  
  
to requirements in Jamroot or individual libraries?

---

## Comment 7

> Username: apolukhin  
> Created_at: 2014-05-22 13:16:32 UTC  
> Url: https://github.com/boostorg/build/pull/12#issuecomment-43885645  

Thanks, I've done something like this in one of the Jamfiles.  
  
Though users will enjoy out-of-the-box solution, I agree that defining project specific macros in toolsets is not good. Closing...

---
