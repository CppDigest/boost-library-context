# #46 Detect the intel compiler in the environement when building bjam.  [Closed]

> Username: aminiussi  
> Created at: 2014-10-21 09:54:44 UTC  
> Updated at: 2021-10-02 22:20:41 UTC  
> Closed at: 2014-11-06 06:56:21 UTC  
> Url: https://github.com/boostorg/build/pull/46  

The mentioned 'standard' path were quite old (9.0, last Intel compiler version is 15.x as on Oct 2014).   
Intel package organization changes quite often these days, so the environment seems like the best place to search  
  
Kept the legacy detection though.  
Build in 64 bit (needs to be specified, 64bit seems more common).

---

## Comment 1

> Username: aminiussi  
> Created_at: 2014-10-21 09:58:36 UTC  
> Url: https://github.com/boostorg/build/pull/46#issuecomment-59904758  

Also, search icc before gcc. For some reason, icc was we only compiler search after gcc. But most platforms with icc also have gcc since Intel compiler uses gnu runtime on linux.

---

## Comment 2

> Username: vprus  
> Created_at: 2014-10-24 07:10:15 UTC  
> Url: https://github.com/boostorg/build/pull/46#issuecomment-60352036  

Thanks for the patch!   
  
I wonder whether it's best to check for gcc first, though. It's just the compiler used to build b2 engine, one can use intel compiler then, and gcc tends to be virtually trouble-free, since it's usually the system compiler?

---

## Comment 3

> Username: aminiussi  
> Created_at: 2014-10-28 09:48:59 UTC  
> Url: https://github.com/boostorg/build/pull/46#issuecomment-60731066  

The reason I did put icc first was that icc was the only exception, so I thought whatever the reasons used for the other compilers also applied for icc.   
I have no strong opinion regarding the overall strategy and yes, having gcc selected first could be a solution to avoid differences between toolsets. But then maybe gcc should be moved at the top of the list ?

---

## Comment 4

> Username: Belcourt  
> Created_at: 2014-10-31 20:31:56 UTC  
> Url: https://github.com/boostorg/build/pull/46#issuecomment-61326650  

I wouldn't like to see gcc first, that would potentially break a couple of our platforms.  Having Intel precede gcc may be okay, but that would have significant repercussions for me as both Intel and Gcc are in our path on some Linux systems and we've been testing with Gcc for years.  This would silently switch all our external testers on Mac and Linux over to Intel for b2, that's a big change and it might impact other testers as well.  
  
So, while I could be persuaded to put Intel first, I'd rather see a separate patch to remove really old Intel paths, say older than Intel v9.0, and probing the environment seems okay.  But leave the current default compiler as GCC rather than changing it to Intel.  
  
I could change our testing scripts if you really want to build b2 with Intel, or you could publish some test results with an Intel b2?

---

## Comment 5

> Username: aminiussi  
> Created_at: 2014-11-03 13:24:43 UTC  
> Url: https://github.com/boostorg/build/pull/46#issuecomment-61476268  

I restored the original order between icc and gcc.  
So there is just the extraction of icc in the user environment. I still can build explicitly with icc.

---

## Comment 6

> Username: vprus  
> Created_at: 2014-11-06 06:56:21 UTC  
> Url: https://github.com/boostorg/build/pull/46#issuecomment-61934579  

Alain,  
  
thanks, I've merged the current version of the patch to develop.

---
