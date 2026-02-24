# #110 - Compilation with compiler v141_xp (Visual Studio 2017 - Windows XP) broken, can be easily fixed [Closed]

> Username: wmatw  
> Created at: 2023-04-30 14:06:53 UTC  
> Updated at: 2023-04-30 22:41:32 UTC  
> Closed at: 2023-04-30 22:40:44 UTC  
> Url: https://github.com/boostorg/system/issues/110  

Hi, in this commit: [8449c62](https://github.com/boostorg/system/commit/8449c62162e497043623c1c872b28f934f1545c9), done by @pdimov , need to add an additional condition for using shared_mutex:  
#ifndef _USING_V110_SDK71_  
  
This def is active when using v141_xp, and according the <shared_mutex> header of STL:  
  
` #ifndef _HAS_SHARED_MUTEX  
  #ifdef _USING_V110_SDK71_  
   #define _HAS_SHARED_MUTEX	0  
  #else /* _USING_V110_SDK71_ */  
   #define _HAS_SHARED_MUTEX	1  
  #endif /* _USING_V110_SDK71_ */  
 #endif /* _HAS_SHARED_MUTEX */  
  
_STD_BEGIN  
#if _HAS_SHARED_MUTEX  
	// CLASS shared_mutex  
class shared_mutex ...`

---

## Comment 1

> Username: wmatw  
> Created at: 2023-04-30 14:19:16 UTC  
> Url: https://github.com/boostorg/system/issues/110#issuecomment-1529036445  

right now, as a workaround in my project, I am defining:  
#undef BOOST_MSSTL_VERSION //ugly hack for supporting v141_xp  
before including the boost header

---

## Comment 2

> Username: pdimov  
> Created at: 2023-04-30 15:20:02 UTC  
> Url: https://github.com/boostorg/system/issues/110#issuecomment-1529049407  

I didn't expect anyone to still use the XP toolsets. :-) Will look into fixing this.

---

## Comment 3

> Username: wmatw  
> Created at: 2023-04-30 19:54:08 UTC  
> Url: https://github.com/boostorg/system/issues/110#issuecomment-1529126111  

To be honest, I wasn't expecting to still support XP in 2022, but their is still a huge demand for maintenance for POS with windows XP embedded devices . Microsoft ended the extended support only in 2019. I believe I will able to drop the support someday...

---

## Comment 4

> Username: pdimov  
> Created at: 2023-04-30 22:41:32 UTC  
> Url: https://github.com/boostorg/system/issues/110#issuecomment-1529156080  

Should be fixed by https://github.com/boostorg/system/commit/32dbf1b99290ced5666f6b1e8390571c02c94c5e.
