# #2 Add support for Apple iOS and improve detection of arm architecture [Merged]

> Username: fdetro  
> Created at: 2014-02-26 11:50:58 UTC  
> Updated at: 2014-07-05 13:49:15 UTC  
> Merged at: 2014-03-20 13:38:29 UTC  
> Closed at: 2014-03-20 13:38:29 UTC  
> Url: https://github.com/boostorg/predef/pull/2  

I've added a new predef `BOOST_OS_IOS` for Apple iOS based on detection of the preprocessor macro `__ENVIRONMENT_IPHONE_OS_VERSION_MIN_REQUIRED__`.  
  
Additionally, I've extended the detection for `BOOST_ARCH_ARM` with a check for `__arm64`, a preprocessor macro which is set by the Apple clang compiler for the new ARMv8 64bit architecture.  
  
Both changes enable the use of `boost::predef` with projects targeting Apple iOS for 32 and 64bit arm architectures.  
  
It would be great if this patch can make it into the next boost release!

---

## Comment 1

> Username: ruslo  
> Created_at: 2014-03-03 10:26:19 UTC  
> Url: https://github.com/boostorg/predef/pull/2#issuecomment-36497441  

Good patch. BTW it would be nice to have ios simulator detection macro too.

---

## Comment 2

> Username: fdetro  
> Created_at: 2014-03-03 13:14:44 UTC  
> Url: https://github.com/boostorg/predef/pull/2#issuecomment-36508651  

iOS simulator detection is already there:  
  
`BOOST_OS_IOS && BOOST_ARCH_ARM` -> iOS device mode  
`BOOST_OS_IOS && BOOST_ARCH_X86` -> iOS simulator mode

---

## Comment 3

> Username: ruslo  
> Created_at: 2014-03-03 13:31:38 UTC  
> Url: https://github.com/boostorg/predef/pull/2#issuecomment-36509804  

Well, it's true (: But I think first people's step will be:  
  
```  
#if BOOST_OS_IOS  
# if BOOST_ARCH_ARM  
#  define MY_MACRO_OS_IOS_DEVICE  
# else  
#  define MY_MACRO_OS_IOS_SIMULATOR  
# endif  
#endif  
```

---

## Comment 4

> Username: fdetro  
> Created_at: 2014-03-03 14:10:23 UTC  
> Url: https://github.com/boostorg/predef/pull/2#issuecomment-36512737  

OK, now I see what you mean. I should probably introduce `BOOST_OS_IOS_DEVICE` and `BOOST_OS_IOS_SIMULATOR`, too.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2014-03-06 03:14:29 UTC  
> Updated_at: 2014-03-12 13:09:33 UTC  
> Url: https://github.com/boostorg/predef/pull/2#discussion_r10330318  

For me to be able to add any new code to a Boost library there must be correct copyright attribution. Please update with your copyright.

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2014-03-06 03:21:26 UTC  
> Updated_at: 2014-03-12 13:09:33 UTC  
> Url: https://github.com/boostorg/predef/pull/2#discussion_r10330454  

This is not the way to do mutual exclusion for OS detection. You need to include the mutual OS header dependencies first at the top. Then only do the check for *_OS_DETECTED here. See the "os/bsd.h" header https://github.com/boostorg/predef/blob/develop/include/boost/predef/os/bsd.h for an example.

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2014-03-06 03:23:14 UTC  
> Updated_at: 2014-03-12 13:09:33 UTC  
> Url: https://github.com/boostorg/predef/pull/2#discussion_r10330485  

Since this is a significant change to the file I need you to add your copyright info for me to accept the changes.

---

## Comment 8

> Username: grafikrobot  
> Created_at: 2014-03-06 03:24:11 UTC  
> Updated_at: 2014-03-12 13:09:33 UTC  
> Url: https://github.com/boostorg/predef/pull/2#discussion_r10330497  

See below for why this line should be removed.

---

## Comment 9

> Username: fdetro  
> Created_at: 2014-03-20 07:43:39 UTC  
> Url: https://github.com/boostorg/predef/pull/2#issuecomment-38141652  

I've changed everything as requested by the review. Is the patch OK now?

---

## Comment 10

> Username: ruslo  
> Created_at: 2014-04-28 05:47:22 UTC  
> Url: https://github.com/boostorg/predef/pull/2#issuecomment-41524780  

Hi, what about `BOOST_OS_IOS_DEVICE` and `BOOST_OS_IOS_SIMULATOR`?

---

## Comment 11

> Username: fdetro  
> Created_at: 2014-04-30 06:55:28 UTC  
> Url: https://github.com/boostorg/predef/pull/2#issuecomment-41766106  

@ruslo: This `DEVICE` vs. `SIMULATOR` case does not really match with the predef `OS` concept, so I left this out for now.  
  
Maybe @grafikrobot can comment on this...

---

## Comment 12

> Username: grafikrobot  
> Created_at: 2014-04-30 14:17:15 UTC  
> Url: https://github.com/boostorg/predef/pull/2#issuecomment-41801320  

There's the concept of "platforms" that would likely fit the different iOS  
targets. Perhaps having "BOOST_PLAT_IOS_DEVICE" and  
"BOOST_PLAT_IOS_SIMULATOR"?  
  
On Wed, Apr 30, 2014 at 1:55 AM, Franz Detro notifications@github.comwrote:  
  
> @ruslo https://github.com/ruslo: This DEVICE vs. SIMULATOR case does  
> not really match with the predef OS concept, so I left this out for now.  
>   
> Maybe @grafikrobot https://github.com/grafikrobot can comment on this...  
>   
> —  
> Reply to this email directly or view it on GitHubhttps://github.com/boostorg/predef/pull/2#issuecomment-41766106  
> .  
  
##   
  
-- Rene Rivera  
-- Grafik - Don't Assume Anything  
-- Robot Dreams - http://robot-dreams.net  
-- rrivera/acm.org (msn) - grafikrobot/aim,yahoo,skype,efnet,gmail

---

## Comment 13

> Username: ruslo  
> Created_at: 2014-05-01 06:32:08 UTC  
> Url: https://github.com/boostorg/predef/pull/2#issuecomment-41884468  

> Perhaps having "BOOST_PLAT_IOS_DEVICE" and "BOOST_PLAT_IOS_SIMULATOR"?  
  
Sounds nice

---
