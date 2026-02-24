# #169 - Fiber launch policy: unlinked [Closed]

> Username: mtanski  
> Created at: 2018-03-08 20:02:25 UTC  
> Updated at: 2020-04-24 09:51:18 UTC  
> Closed at: 2020-04-24 09:51:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/169  

Right now when a fiber is a created it's either launched right away or put in the run queue on the current scheduler. In some cases it I may need to delay launching the fiber (because I want to start it on a different scheduler like CPU bound work scheduler).  
  
This might be easily done if there's a policy that doesn't link it to the current scheduler / doesn't put it in the run queue. This way it can be manually added to another scheduler.

---

## Comment 1

> Username: olk  
> Created at: 2018-04-11 04:26:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/169#issuecomment-380323267  

Ty for your suggestion, I've to think about it - but I'm busy at the moment

---

## Comment 2

> Username: olk  
> Created at: 2020-04-24 09:51:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/169#issuecomment-618916393  

it is not possible with the current design (schedulers are implicit at the moment)
