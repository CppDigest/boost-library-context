# #464 Install BoostComputeConfig.cmake to lib/cmake/BoostCompute [Merged]

> Username: marbre  
> Created at: 2015-06-06 08:22:01 UTC  
> Updated at: 2015-06-12 15:54:27 UTC  
> Merged at: 2015-06-09 03:53:41 UTC  
> Closed at: 2015-06-09 03:53:41 UTC  
> Url: https://github.com/boostorg/compute/pull/464  

I am packaging BoostCompute for Gentoo Linux. Normally on Linux systems cmake configurations go to /usr/lib/cmake, while Find*.cmake modules go to /usr/share/cmale/Modules. I assume the installation path should be changed.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-06-09 03:53:48 UTC  
> Url: https://github.com/boostorg/compute/pull/464#issuecomment-110216300  

Thanks for fixing this!

---

## Comment 2

> Username: ghisvail  
> Created_at: 2015-06-12 13:43:33 UTC  
> Updated_at: 2015-06-12 14:47:22 UTC  
> Url: https://github.com/boostorg/compute/pull/464#issuecomment-111496681  

> Normally on Linux systems cmake configurations go to /usr/lib/cmake  
  
Please rephrase this with "on my Linux system", i.e. Gentoo. For other Linux systems like those which are Debian-based this path would be /usr/lib/$(multiarch-triplet)/cmake, for arch-based systems it would be /usr/lib32/cmake or /usr/lib64/cmake, and so on...  
  
Please refrain from making such generic assertion @marbre in the future, where all you propose is a patch that addresses _your_ distribution specific needs.  
  
Downstream specific changes should stick downstream, period.  
  
Apologize for the dry tone.  
  
EDIT: "<"multiarch-triplet">" -> $(multiarch-triplet)

---

## Comment 3

> Username: ghisvail  
> Created_at: 2015-06-12 13:49:09 UTC  
> Url: https://github.com/boostorg/compute/pull/464#issuecomment-111498003  

Slight erratum here:  
  
> for arch-based systems it would be /usr/lib32/cmake or /usr/lib64/cmake  
  
For Arch-Linux 32-bits: /usr/lib32  
For Arch-Linux 64-bits: /usr/lib  
For Fedora 64-bits: /usr/lib64

---

## Comment 4

> Username: pavanky  
> Created_at: 2015-06-12 13:50:30 UTC  
> Updated_at: 2015-06-12 13:53:32 UTC  
> Url: https://github.com/boostorg/compute/pull/464#issuecomment-111498629  

@ghisvail **on arch** /usr/lib32 is for 32 bit libraries on 64 bit systems. It is /usr/lib on 32 bit systems.

---

## Comment 5

> Username: ghisvail  
> Created_at: 2015-06-12 14:00:12 UTC  
> Updated_at: 2015-06-12 14:21:48 UTC  
> Url: https://github.com/boostorg/compute/pull/464#issuecomment-111501880  

My point remains: on Linux, as in "general" Linux, the path where the lib and cmake stuff will eventually land is _downstream specific_. Hence a patch like this solves nothing but one specific case.  
  
FYI, I have no particular feeling towards the acceptance of this patch, but justifying it in the name of "generic" Linux is a bit upsetting.  
  
EDIT: typo fix

---

## Comment 6

> Username: pavanky  
> Created_at: 2015-06-12 14:08:01 UTC  
> Url: https://github.com/boostorg/compute/pull/464#issuecomment-111505888  

I understand, I just pointed out the 32 bit stuff because you specifically corrected yourself with another comment. Ideally if there was an environment variable that linux distributions used for the library path, we can just use that instead.

---

## Comment 7

> Username: ghisvail  
> Created_at: 2015-06-12 14:22:24 UTC  
> Url: https://github.com/boostorg/compute/pull/464#issuecomment-111510915  

> I just pointed out the 32 bit stuff because you specifically corrected yourself with another comment.  
  
Thanks for correcting me.

---

## Comment 8

> Username: marbre  
> Created_at: 2015-06-12 14:59:37 UTC  
> Url: https://github.com/boostorg/compute/pull/464#issuecomment-111518975  

As far as I get it [FHS](http://www.pathname.com/fhs/pub/fhs-2.3.html#USRLIBLIBRARIESFORPROGRAMMINGANDPA) defines `/usr/lib` as mandatory, while `/usr/lib<qual>` is optional. IMHO on multilib systems `/usr/lib<qual>` linked to `/usr/lib`.  
This was not a justification, it was more an explanation. As I do this in my free time , not even needing it for my research, I might sometimes hold it short. Anyway, sorry for the inconveniences.

---

## Comment 9

> Username: ghisvail  
> Created_at: 2015-06-12 15:54:27 UTC  
> Url: https://github.com/boostorg/compute/pull/464#issuecomment-111534018  

> This was not a justification, it was more an explanation.  
  
My apologize for misinterpreting your intentions.   
  
> As I do this in my free time , not even needing it for my research  
  
That makes two of us.

---
