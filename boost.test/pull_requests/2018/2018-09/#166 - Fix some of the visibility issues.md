# #166 Fix some of the visibility issues [Closed]

> Username: apolukhin  
> Created at: 2018-09-18 19:12:38 UTC  
> Updated at: 2018-09-24 18:10:21 UTC  
> Closed at: 2018-09-24 18:10:21 UTC  
> Url: https://github.com/boostorg/test/pull/166  



---

## Comment 1

> Username: apolukhin  
> Created_at: 2018-09-19 08:15:38 UTC  
> Updated_at: 2018-09-22 18:40:16 UTC  
> Url: https://github.com/boostorg/test/pull/166#issuecomment-422703568  

~~Do not forget to put `visibility=global` [here](https://github.com/boostorg/test/blob/4df5b095eaa967f5e2687978bd3d9467b758f28e/example/Jamfile.v2#L18) and [here](https://github.com/boostorg/test/blob/4df5b095eaa967f5e2687978bd3d9467b758f28e/test/Jamfile.v2#L18)~~  
  
~~Or force the shared library singleton instances usage by some code magic, like `singleton<type>::instance().variable`.~~  
  
No need to do that ^  
Now everything just works.

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2018-09-24 18:10:21 UTC  
> Url: https://github.com/boostorg/test/pull/166#issuecomment-424071214  

Superseded by rev 49cd9a17847aa24a925749a3d39ffc045e9fb761 (getting rid of the broken singleton class).  
  
Thanks for your support with this issue!

---
