# #1305 - circle ci is broken after modular build PR merged [Closed]

> Username: vissarion  
> Created at: 2024-09-06 12:24:19 UTC  
> Updated at: 2024-09-09 09:33:45 UTC  
> Closed at: 2024-09-06 13:33:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/1305  

@grafikrobot @barendgehrels I noticed a break in circleCI builds after merging https://github.com/boostorg/geometry/pull/1292

---

## Comment 1

> Username: grafikrobot  
> Created at: 2024-09-06 12:41:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1305#issuecomment-2333968181  

Hm.. Looks like depinst is not installing foreach.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2024-09-06 12:44:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/1305#issuecomment-2333972951  

PS. But it doesn't look like it's needed for the tests any longer. It's only needed for the examples.

---

## Comment 3

> Username: vissarion  
> Created at: 2024-09-09 09:33:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/1305#issuecomment-2337615341  

thanks for the quick fix @grafikrobot
