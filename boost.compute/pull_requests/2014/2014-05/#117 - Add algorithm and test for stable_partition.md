# #117 Add algorithm and test for stable_partition [Merged]

> Username: roshanrags  
> Created at: 2014-05-22 19:41:30 UTC  
> Updated at: 2014-05-28 21:58:45 UTC  
> Merged at: 2014-05-24 01:34:31 UTC  
> Closed at: 2014-05-24 01:34:31 UTC  
> Url: https://github.com/boostorg/compute/pull/117  

Added algorithm and test for `stable_partition` and changed `partition` to use it for now. I am searching for a good implementation(swaps based?) for `partition` to take advantage of the fact that order of elements need not be preserved.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-05-23 06:14:36 UTC  
> Url: https://github.com/boostorg/compute/pull/117#issuecomment-43974509  

Looks good!  
  
One minor issue though, can you change the commit message to wrap at 60-70 columns? Long lines in commit messages make it harder to look through the logs in a smaller terminal.  
  
After that it should be good to merge.

---

## Comment 2

> Username: roshanrags  
> Created_at: 2014-05-23 09:07:27 UTC  
> Url: https://github.com/boostorg/compute/pull/117#issuecomment-43986915  

Done!

---

## Comment 3

> Username: coveralls  
> Created_at: 2014-05-23 09:21:44 UTC  
> Url: https://github.com/boostorg/compute/pull/117#issuecomment-43988068  

[![Coverage Status](https://coveralls.io/builds/798087/badge)](https://coveralls.io/builds/798087)  
  
Coverage increased (+0.01%) when pulling **4d3d114285522e24d444f6235710c9a54a2f407e on roshanr95:stable_partition** into **4a507cb3681f8e87f07ec6518ac2be3d46c6ebd0 on kylelutz:develop**.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-05-24 01:34:39 UTC  
> Url: https://github.com/boostorg/compute/pull/117#issuecomment-44074187  

Thanks! Merged!

---
