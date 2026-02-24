# #115 fix cpu_relax.h for compilation with Intel compiler on Windows [Merged]

> Username: mibintc  
> Created at: 2017-03-10 14:18:20 UTC  
> Updated at: 2017-03-10 16:30:32 UTC  
> Merged at: 2017-03-10 16:22:59 UTC  
> Closed at: 2017-03-10 16:22:59 UTC  
> Url: https://github.com/boostorg/fiber/pull/115  

Hello,  
I'm trying to build the boost 1.63 library with the Intel 17.0 compiler (latest update) in the vs2015 environment. Several targets fail to build because the definition of the cpu_relax macro isn't correct.  
  
This change corrects the problem and allows the build to complete successfully.  Is this the right way to correct the problem? At Intel, we like to set up the Intel compiler so the macro symbols emulate our "host" compiler, cl.exe on Windows and gcc on Linux. This way whatever work folks put into making boost (or some other project) work for cl.exe should also work for the Intel compiler (or it would be a compiler bug that should be fixed)  
  
 Without this change I get a message like this:  
  
./boost/fiber/detail/spinlock_ttas.hpp(66): error: invalid constant in assembly language instruction  
                      asm volatile ("pause" ::: "memory");;  
                                    ^  
  
This change allows the macro to compile to YieldProcessor()  
  
I work for Intel, on the Intel C++ compiler.  
  
Thanks and regards, Melanie Blower

---

## Comment 1

> Username: olk  
> Created_at: 2017-03-10 16:29:17 UTC  
> Url: https://github.com/boostorg/fiber/pull/115#issuecomment-285715446  

Hello Melanie, I've pushed your patch to branch develop - ty

---

## Comment 2

> Username: mibintc  
> Created_at: 2017-03-10 16:30:32 UTC  
> Url: https://github.com/boostorg/fiber/pull/115#issuecomment-285715795  

Got that. Thanks so much!  
  
From: Oliver Kowalke [mailto:notifications@github.com]  
Sent: Friday, March 10, 2017 11:29 AM  
To: boostorg/fiber <fiber@noreply.github.com>  
Cc: Blower, Melanie <melanie.blower@intel.com>; Author <author@noreply.github.com>  
Subject: Re: [boostorg/fiber] fix cpu_relax.h for compilation with Intel compiler on Windows (#115)  
  
  
Hello Melanie, I've pushed your patch to branch develop - ty  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/fiber/pull/115#issuecomment-285715446>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AJbqBl8rbiCpY1QuoXsAtJ6Ih-Pgp4rLks5rkXpegaJpZM4MZbv->.

---
