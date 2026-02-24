# #296 adding new example for string class [Closed]

> Username: Mageswaran1989  
> Created at: 2014-11-05 05:24:02 UTC  
> Updated at: 2014-11-07 16:26:22 UTC  
> Closed at: 2014-11-07 16:26:22 UTC  
> Url: https://github.com/boostorg/compute/pull/296  

Please review.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-11-05 05:39:45 UTC  
> Url: https://github.com/boostorg/compute/pull/296#issuecomment-61762407  

[![Coverage Status](https://coveralls.io/builds/1429550/badge)](https://coveralls.io/builds/1429550)  
  
Coverage remained the same when pulling **db6fb1239e0cb94a70a4e35371f4908cfb515eda on Mageswaran1989:string-example** into **4c0c7bdde7c8b43462a326eb90aa105a3b403669 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-11-05 15:58:21 UTC  
> Updated_at: 2014-11-05 18:37:44 UTC  
> Url: https://github.com/boostorg/compute/pull/296#discussion_r19882084  

looks like you're missing a `return` at the end of `main()`

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-11-06 04:00:42 UTC  
> Url: https://github.com/boostorg/compute/pull/296#issuecomment-61924431  

Can you fix the following errors (see the Travis-CI build):  
  
```  
/home/travis/build/kylelutz/compute/example/string.cpp: In function ‘int main(int, char**)’:  
/home/travis/build/kylelutz/compute/example/string.cpp:59:34: error: comparison between signed and unsigned integer expressions [-Werror=sign-compare]  
/home/travis/build/kylelutz/compute/example/string.cpp:78:38: error: comparison between signed and unsigned integer expressions [-Werror=sign-compare]  
```  
  
Also, when you update your pull-request with fixes for any review comments, it's easier to just amend the initial commit (with `git commit -a --amend`) rather than add additional commits.  
  
Thanks!

---

## Comment 4

> Username: Mageswaran1989  
> Created_at: 2014-11-06 05:08:01 UTC  
> Url: https://github.com/boostorg/compute/pull/296#issuecomment-61928219  

working on swap method. I will fix these warnings and push it along with swap

---
