# #456 Fix derivative of pow(x,y) when x=0 [Merged]

> Username: pulver  
> Created at: 2020-11-11 22:36:20 UTC  
> Updated at: 2020-11-16 17:14:27 UTC  
> Merged at: 2020-11-16 16:52:29 UTC  
> Closed at: 2020-11-16 16:52:29 UTC  
> Url: https://github.com/boostorg/math/pull/456  

Fixes #445

---

## Comment 1

> Username: pulver  
> Created_at: 2020-11-15 14:21:31 UTC  
> Url: https://github.com/boostorg/math/pull/456#issuecomment-727577287  

All of the Travis and Appveyor CI tests passed for this PR except for one Appveyor test:  
![image](https://user-images.githubusercontent.com/39707/99187210-40d56e80-2723-11eb-8e3b-8aa98c814abc.png)  
https://ci.appveyor.com/project/jzmaddock/math/builds/36262373  
apparently due to a 1-hour timeout. In contrast, a [recent sucessful build]( https://ci.appveyor.com/project/jzmaddock/math/builds/36294269) took just under 58 minutes for that same test.  
  
Suggestions?

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-11-16 12:41:55 UTC  
> Url: https://github.com/boostorg/math/pull/456#issuecomment-727954319  

@pulver : That happens on occasion; it's nothing to worry about.  
  
Is it ready to merge?

---

## Comment 3

> Username: pulver  
> Created_at: 2020-11-16 13:30:46 UTC  
> Url: https://github.com/boostorg/math/pull/456#issuecomment-728017152  

> Is it ready to merge?  
  
Yes.

---

## Review 4 [Commented]

> Username: wgledbetter  
> Created_at: 2020-11-16 17:14:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/456#pullrequestreview-531533771  

Looks good to me. Thanks for being responsive!

---
