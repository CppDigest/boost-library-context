# #36 Work around lack of thread safety in cygwin CLOCK_MONOTONIC one-time initialization [Merged]

> Username: jeking3  
> Created at: 2018-11-23 17:02:08 UTC  
> Updated at: 2018-11-26 20:55:10 UTC  
> Merged at: 2018-11-25 07:56:31 UTC  
> Closed at: 2018-11-25 07:56:31 UTC  
> Url: https://github.com/boostorg/chrono/pull/36  

Observation:  Two threads calling boost::chrono::high_resolution_timer::now() on cygwin at roughly the same time will sometimes return a timespec of all zero.  
  
Root cause: cygwin has no thread safety in their one-time hires_ns timer initialization.  
  
I sent a message to their mailing list about it with an example (attached).  
  
[cygwinrace.zip](https://github.com/boostorg/chrono/files/2611353/cygwinrace.zip)  
  
This fixes #35   
  
I will apply the same fix to Boost.DateTime if necessary.  Once this is merged into develop we can continue qualifying appveyor improvements.

---

## Comment 1

> Username: viboes  
> Created_at: 2018-11-24 12:41:12 UTC  
> Url: https://github.com/boostorg/chrono/pull/36#issuecomment-441365263  

Thanks for the report and the fix.  
Do you mind if we merge this after boost-1.70?

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-11-24 13:12:34 UTC  
> Url: https://github.com/boostorg/chrono/pull/36#issuecomment-441367005  

I assume you meant 1.69.0, and you can merge this into develop now without harm.  I am using a temporary workaround in my CI branch of thread that pulls this commit to get around the cygwin issues on appveyor for thread CI.  I found one call like this in Boost.Thread as well and I have added the same fix to it (in the CI fork).

---

## Comment 3

> Username: viboes  
> Created_at: 2018-11-25 07:56:36 UTC  
> Url: https://github.com/boostorg/chrono/pull/36#issuecomment-441422373  

Yes, it is boost-1.69.  
Ok, I see. I've merged it. Hopping we will not have any trouble in boost-1.69.

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-11-26 20:55:10 UTC  
> Url: https://github.com/boostorg/chrono/pull/36#issuecomment-441794562  

This issue was reproduced independently upstream and a fix is in the works, but we will need to keep this workaround in the code to deal with existing older cygwin builds.  
  
https://cygwin.com/ml/cygwin/2018-11/msg00190.html

---
