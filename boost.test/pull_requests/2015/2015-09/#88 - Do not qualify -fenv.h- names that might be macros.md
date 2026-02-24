# #88 Do not qualify <fenv.h> names that might be macros. [Merged]

> Username: jwakely  
> Created at: 2015-09-21 10:35:51 UTC  
> Updated at: 2015-12-22 08:56:56 UTC  
> Merged at: 2015-11-08 06:55:55 UTC  
> Closed at: 2015-11-08 06:55:55 UTC  
> Url: https://github.com/boostorg/test/pull/88  

This fixes a build failure for GNU/Linux on powerpc, as described at https://bugzilla.redhat.com/show_bug.cgi?id=1262444  
  
This is the same problem described at https://svn.boost.org/trac/boost/ticket/6767

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2015-11-03 13:06:27 UTC  
> Url: https://github.com/boostorg/test/pull/88#issuecomment-153346929  

Do you know if the issue https://svn.boost.org/trac/boost/ticket/11756 is also related to this one?

---

## Comment 2

> Username: daminetreg  
> Created_at: 2015-12-18 13:37:16 UTC  
> Url: https://github.com/boostorg/test/pull/88#issuecomment-165780316  

Is there any plan to merge this for 1.61 again ?

---

## Comment 3

> Username: rogeeff  
> Created_at: 2015-12-22 06:14:54 UTC  
> Url: https://github.com/boostorg/test/pull/88#issuecomment-166524766  

I thought  I merged this into develop in early november.  
  
On Fri, Dec 18, 2015 at 8:37 AM, Damien Buhl notifications@github.com  
wrote:  
  
> Is there any plan to merge this for 1.61 again ?  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/88#issuecomment-165780316.  
  
##   
  
Gennadiy Rozental

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2015-12-22 06:47:13 UTC  
> Url: https://github.com/boostorg/test/pull/88#issuecomment-166531404  

I reverted it to maintain the scope of 1.60. Now that 1.60 is released, I merged the change to develop.

---

## Comment 5

> Username: daminetreg  
> Created_at: 2015-12-22 08:56:56 UTC  
> Url: https://github.com/boostorg/test/pull/88#issuecomment-166557840  

Thanks. We use the patch succesfully on top of 1.59.0 on ppc603e-poky-linux, armv7a-vfp-neon-poky-linux-gnueabi and x86_64 linux without any problem. Hope 1.61.0 will have it. :smile:

---
