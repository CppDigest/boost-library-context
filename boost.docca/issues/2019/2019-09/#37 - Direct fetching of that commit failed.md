# #37 - Direct fetching of that commit failed [Closed]

> Username: rcorbin2018  
> Created at: 2019-09-16 20:05:46 UTC  
> Updated at: 2019-09-17 00:08:36 UTC  
> Closed at: 2019-09-17 00:08:36 UTC  
> Url: https://github.com/boostorg/docca/issues/37  

Trying to build rippled and got the following today 9/16/2019:  
  
error: Server does not allow request for unadvertised object 335dbf9c3613e997ed56d540cc8c5ff2e28cab2d  
Fetched in submodule path 'doc/docca', but it did not contain 335dbf9c3613e997ed56d540cc8c5ff2e28cab2d. Direct fetching of that commit failed.  
  
Did the above docca commit object exist before and is gone now? What am I missing?  
  
Thanks.  
  
-Rob

---

## Comment 1

> Username: rcorbin2018  
> Created at: 2019-09-16 21:12:47 UTC  
> Url: https://github.com/boostorg/docca/issues/37#issuecomment-531960431  

I can see the commit when I click the link in my comment above, but that commit is not showing up as a tree in the commits tab. After I clone the project, I run:  
  
git checkout 335dbf9c3613e997ed56d540cc8c5ff2e28cab2d  
fatal: reference is not a tree: 335dbf9c3613e997ed56d540cc8c5ff2e28cab2d  
  
Is this tree being hidden by permissions or something?  
  
Thanks.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-09-16 23:39:28 UTC  
> Url: https://github.com/boostorg/docca/issues/37#issuecomment-531998637  

Does rippled even use **docca** anymore? I rewrote the branch, which is why you can't find the commit. Where does rippled use docca?

---

## Comment 3

> Username: rcorbin2018  
> Created at: 2019-09-16 23:50:06 UTC  
> Url: https://github.com/boostorg/docca/issues/37#issuecomment-532000739  

rippled uses NuDB which uses docca as a submodule. When building rippled from source, it does a git clone on NuDB and it’s sub modules.  
  
Thanks.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-09-16 23:54:20 UTC  
> Url: https://github.com/boostorg/docca/issues/37#issuecomment-532001609  

Yeah, I forgot that NuDB used docca in a submodule. I have restored the branch that was removed, so if you try again it should work.

---

## Comment 5

> Username: rcorbin2018  
> Created at: 2019-09-17 00:08:36 UTC  
> Url: https://github.com/boostorg/docca/issues/37#issuecomment-532004696  

It's working now.  
  
Thank you. I appreciate it.
