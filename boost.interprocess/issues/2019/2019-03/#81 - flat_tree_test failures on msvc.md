# #81 - flat_tree_test failures on msvc [Closed]

> Username: Kojoley  
> Created at: 2019-03-20 23:37:55 UTC  
> Updated at: 2019-03-23 17:55:39 UTC  
> Closed at: 2019-03-23 17:55:39 UTC  
> Url: https://github.com/boostorg/interprocess/issues/81  

You can observe failures in interprocess flat_tree_test test on [regression matrix](https://www.boost.org/development/tests/develop/developer/interprocess.html) on msvc runners. It started failing after https://github.com/boostorg/container/commit/6ce2b2d0f896d5c4d2c1d071a2908e1aea0311f5 (when I `git checkout 6ce2b2d0f896d5c4d2c1d071a2908e1aea0311f5^1` the test pass and with `git checkout 6ce2b2d0f896d5c4d2c1d071a2908e1aea0311f5` it fails). I am not sure if a bug in interprocess or in container, but the fail happens only on 64bit (`.\b2 address-model=64 toolset=msvc-14.1 libs/interprocess/test//flat_tree_test`) making me to think it is most likely in interprocess.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-03-21 22:51:57 UTC  
> Url: https://github.com/boostorg/interprocess/issues/81#issuecomment-475432606  

It's definitely a bug. It looks like it's caused by a Boost.Move bug in the auxiliary rotate_adaptive algorithm. The following commit should fix it:  
  
https://github.com/boostorg/move/commit/756c964798ff1110406fab541b85267aefb6fb6c

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-03-23 17:55:39 UTC  
> Url: https://github.com/boostorg/interprocess/issues/81#issuecomment-475890906  

The https://github.com/boostorg/move/commit/756c964798ff1110406fab541b85267aefb6fb6c fixed the issue. Thanks.
