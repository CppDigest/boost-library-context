# #13 Introduced VxWorks Simulator root and corrected canonical for VxWorks [Closed]

> Username: RogerioDosSantos  
> Created at: 2015-04-29 20:12:55 UTC  
> Updated at: 2019-04-16 09:21:41 UTC  
> Closed at: 2019-04-16 09:21:41 UTC  
> Url: https://github.com/boostorg/filesystem/pull/13  

01 - Introduced the root path for the VxWorks simulator.  
It the "host:" is not handled, this can cause infinite recursive call on  
VxWorks simulator when using the canonical function  
  
02 - Corrected path canonical function to handle properly path existence in  
some platforms where the root separator might not be a valid path.  
E.g.: On VxWorks the path "/" can be invalid but the path "/tgtsvr/" is  
a valid path.

---

## Comment 1

> Username: RogerioDosSantos  
> Created_at: 2016-02-15 08:12:14 UTC  
> Url: https://github.com/boostorg/filesystem/pull/13#issuecomment-184107215  

It is more than one year I requested this one. I am wondering when this will be evaluated and which additional steps should I do for those changes to be past of the boost release.

---

## Comment 2

> Username: RogerioDosSantos  
> Created_at: 2018-01-20 02:28:23 UTC  
> Url: https://github.com/boostorg/filesystem/pull/13#issuecomment-359138188  

03 years passed and not even a feedback on this item. What should I do for this item to be approved?

---

## Comment 3

> Username: Lastique  
> Created_at: 2018-11-24 18:14:17 UTC  
> Url: https://github.com/boostorg/filesystem/pull/13#issuecomment-441385814  

If this is still relevant, please, rebase against the current develop.  
  
Also, I don't quite understand the change to `canonical`, although I didn't dig into the code deeply yet. What I'm concerned about is whether this change is needed specifically for VxWorks and noone else. If it's VxWorks-specific then it should be guarded with a platform macro check.

---

## Comment 4

> Username: Lastique  
> Created_at: 2019-04-16 09:21:41 UTC  
> Url: https://github.com/boostorg/filesystem/pull/13#issuecomment-483581386  

No activity, so considering this is not relevant.

---
