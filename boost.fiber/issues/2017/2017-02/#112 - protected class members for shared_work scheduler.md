# #112 - protected class members for shared_work scheduler [Closed]

> Username: danichcr72  
> Created at: 2017-02-23 19:34:15 UTC  
> Updated at: 2017-04-30 19:23:22 UTC  
> Closed at: 2017-04-30 19:21:43 UTC  
> Url: https://github.com/boostorg/fiber/issues/112  

Hi,  
  
  First of all, thanks for the great contributions of fibers to the boost libraries!  
  
  I used fibers to build a runtime system for a distributed database system and had the need to create a custom scheduler.  The scheduler that I needed was pretty similar to your provided shared_work scheduler with the caveat that the "system" or dispatcher fibers must be scheduled with some frequency to garbage-collect terminated tasks.  I proceeded to derive my scheduler from your shared_work but unfortunately needed to change your code to declare the members of the shared_work class to be **protected** instead of **private**.  
  
  Would it be possible to change these class members to be protected so that they can be accessed by derived classes?  
  
Thanks,  
  
Daniel Chavarria  
Trovares Inc.

---

## Comment 1

> Username: olk  
> Created at: 2017-02-24 08:56:18 UTC  
> Url: https://github.com/boostorg/fiber/issues/112#issuecomment-282239002  

I plan to re-factor the API for custom-schedulers - but unfortunately I'm very busy.

---

## Comment 2

> Username: olk  
> Created at: 2017-04-02 07:55:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/112#issuecomment-290971026  

related to #113

---

## Comment 3

> Username: olk  
> Created at: 2017-04-30 19:21:43 UTC  
> Updated at: 2017-04-30 19:23:22 UTC  
> Url: https://github.com/boostorg/fiber/issues/112#issuecomment-298251279  

Please, use work_stealing scheduler instead of shared_work. work_stealing is much faster and does schedule dispatcher fibers correctly.  
But could you show me your modifications, please?
