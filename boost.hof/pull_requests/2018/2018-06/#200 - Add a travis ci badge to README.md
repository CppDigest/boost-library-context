# #200 Add a travis ci badge to README [Merged]

> Username: shreyans800755  
> Created at: 2018-06-09 14:23:28 UTC  
> Updated at: 2018-06-13 18:58:37 UTC  
> Merged at: 2018-06-13 01:17:47 UTC  
> Closed at: 2018-06-13 01:17:47 UTC  
> Url: https://github.com/boostorg/hof/pull/200  

Fixes https://github.com/boostorg/hof/issues/49

---

## Comment 1

> Username: shreyans800755  
> Created_at: 2018-06-09 14:40:10 UTC  
> Url: https://github.com/boostorg/hof/pull/200#issuecomment-395974387  

@pfultz2 Can you please review this ?  
I can also add appveyor badge. But I don't have access to the image link.

---

## Review 2 [Commented]

> Username: pfultz2  
> Created_at: 2018-06-09 15:57:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hof/pull/200#pullrequestreview-127371222  

📁 .gitignore

```diff
   1 |+ doc/
```

> Username: pfultz2  
> Created_at: 2018-06-09 15:57:01 UTC  
> Updated_at: 2018-06-12 21:28:27 UTC  
> Url: https://github.com/boostorg/hof/pull/200#discussion_r194231829  

Doc directory should not be ignored by git.

> Username: shreyans800755  
> Created_at: 2018-06-09 16:17:06 UTC  
> Updated_at: 2018-06-12 21:28:27 UTC  
> Url: https://github.com/boostorg/hof/pull/200#discussion_r194232317  

Its generated for local docs, no ?

> Username: pfultz2  
> Created_at: 2018-06-09 16:35:28 UTC  
> Updated_at: 2018-06-12 21:28:27 UTC  
> Url: https://github.com/boostorg/hof/pull/200#discussion_r194232829  

No, only doc/html is I believe.


---

## Comment 3

> Username: pfultz2  
> Created_at: 2018-06-09 16:35:58 UTC  
> Url: https://github.com/boostorg/hof/pull/200#issuecomment-395982471  

Also, it would be better to put the gitignore changes in a separate PR.

---

## Comment 4

> Username: shreyans800755  
> Created_at: 2018-06-09 19:27:20 UTC  
> Url: https://github.com/boostorg/hof/pull/200#issuecomment-395992940  

> Also, it would be better to put the gitignore changes in a separate PR.  
  
Removed gitignore. I'll create another issue and create a new PR with that.  
  
If you can give me the appveyor image link, I can add it here. And we can have both the badges.

---

## Comment 5

> Username: pfultz2  
> Created_at: 2018-06-11 02:25:56 UTC  
> Url: https://github.com/boostorg/hof/pull/200#issuecomment-396105619  

> If you can give me the appveyor image link  
  
Where do I find that?

---

## Comment 6

> Username: shreyans800755  
> Created_at: 2018-06-11 21:13:15 UTC  
> Url: https://github.com/boostorg/hof/pull/200#issuecomment-396389169  

@pfultz2   
Go to appveyor site -> login -> Settings (Image 1) -> Badges (Image 2) -> URL (Image 2)  
  
Image 1:  
![drawing](https://user-images.githubusercontent.com/3727351/41257301-f37932ce-6de9-11e8-97b9-1dd23fe894b6.png)  
  
  
Image 2:  
![drawing 1](https://user-images.githubusercontent.com/3727351/41257403-524371fc-6dea-11e8-9b12-4fd178c340bf.png)

---

## Comment 7

> Username: pfultz2  
> Created_at: 2018-06-12 04:04:38 UTC  
> Url: https://github.com/boostorg/hof/pull/200#issuecomment-396459145  

This is the URL:  
  
https://ci.appveyor.com/api/projects/status/bjj27h3v3bxbgpsp/branch/develop

---

## Comment 8

> Username: shreyans800755  
> Created_at: 2018-06-12 21:28:46 UTC  
> Url: https://github.com/boostorg/hof/pull/200#issuecomment-396740498  

@pfultz2   
Done.  
You can view it here - https://github.com/shreyans800755/hof/tree/travis

---

## Comment 9

> Username: pfultz2  
> Created_at: 2018-06-13 01:17:40 UTC  
> Url: https://github.com/boostorg/hof/pull/200#issuecomment-396783038  

Awesome thanks.

---
