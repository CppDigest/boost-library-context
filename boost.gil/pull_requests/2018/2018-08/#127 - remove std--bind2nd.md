# #127 remove std::bind2nd [Merged]

> Username: gmoryes  
> Created at: 2018-08-17 11:21:47 UTC  
> Updated at: 2018-08-30 14:05:56 UTC  
> Merged at: 2018-08-21 13:40:56 UTC  
> Closed at: 2018-08-21 13:40:56 UTC  
> Url: https://github.com/boostorg/gil/pull/127  

Fixes #127

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2018-08-17 12:32:49 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/127#pullrequestreview-147206360  

LGTM, assuming Travis CI will be as green as AppVeyor.  
  
Thanks @gmoryes !

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-08-17 19:03:13 UTC  
> Url: https://github.com/boostorg/gil/pull/127#issuecomment-413960493  

It looks we are experiencing delay on Travis CI and we can't do anything about it, I'm afraid. We need to wait.  
  
![image](https://user-images.githubusercontent.com/80741/44284167-f1ace480-a260-11e8-96ce-e80668e4acf0.png)

---

## Comment 3

> Username: gmoryes  
> Created_at: 2018-08-21 13:28:07 UTC  
> Url: https://github.com/boostorg/gil/pull/127#issuecomment-414673440  

Seems all tests passed, what about merging? :)

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2018-08-21 13:41:07 UTC  
> Url: https://github.com/boostorg/gil/pull/127#issuecomment-414677701  

There you go ! :-)  
Many thanks !

---

## Comment 5

> Username: stefanseefeld  
> Created_at: 2018-08-21 13:42:29 UTC  
> Url: https://github.com/boostorg/gil/pull/127#issuecomment-414678193  

Actually, I just now realize that you sent a PR against master. You should always submit PRs against the develop branch instead. My fault for not catching this earlier. Not a problem for this time, but please remember for future contributions. :-)

---

## Comment 6

> Username: mloskot  
> Created_at: 2018-08-21 13:49:32 UTC  
> Url: https://github.com/boostorg/gil/pull/127#issuecomment-414680573  

Ouch, I should have checked that myself too. Sorry @stefanseefeld !  
  
@gmoryes It's always a good idea to skim https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md  
> Regardless if you decide to develop again master (recommended) or develop branch of the Boost super-project, _you should always base your work (ie. topic branches) on Boost.GIL **develop** branch_.

---
