# #105 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74 [Merged]

> Username: eldiener  
> Created at: 2020-04-19 20:03:35 UTC  
> Updated at: 2020-06-17 18:15:38 UTC  
> Merged at: 2020-06-17 18:15:38 UTC  
> Closed at: 2020-06-17 18:15:38 UTC  
> Url: https://github.com/boostorg/regex/pull/105  



---

## Comment 1

> Username: eldiener  
> Created_at: 2020-05-06 04:18:26 UTC  
> Url: https://github.com/boostorg/regex/pull/105#issuecomment-624433227  

The failure on Travis has nothing to do with these changes. I do not understand the failure on AppVeyor as it refers to something in boost/core which does not correspond to anything I have locally.

---

## Comment 2

> Username: eldiener  
> Created_at: 2020-05-08 18:11:20 UTC  
> Url: https://github.com/boostorg/regex/pull/105#issuecomment-625946377  

How do I retrigger the travis and/or appveyor tests without making a further change to the PR ? The header file from boost.core that was causing the appveyor failure has been updated since the test and the travis failure looks like it has something to do with a travis failure.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2020-05-08 18:26:22 UTC  
> Url: https://github.com/boostorg/regex/pull/105#issuecomment-625952727  

For appveyor there's a "Re run incomplete" button which restarts failed runs.  Maybe only the repro owner sees it though?  I've restarted though...

---

## Comment 4

> Username: eldiener  
> Created_at: 2020-05-08 18:31:27 UTC  
> Url: https://github.com/boostorg/regex/pull/105#issuecomment-625954884  

Where is this "Re run incomplete" button supposed to be ? I do not see it on the Github page next to the CI test for the particular "check", so maybe you are right that only the repro owner is able to do it. It does seem odd that the person issuing the PR can not rerun some CI test. Thanks for rerunning the tests.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2020-05-08 18:40:17 UTC  
> Url: https://github.com/boostorg/regex/pull/105#issuecomment-625958665  

![screen2](https://user-images.githubusercontent.com/5011768/81437826-b3a5d180-9163-11ea-8d0f-f677362702e5.png)  
![screen1](https://user-images.githubusercontent.com/5011768/81437838-b6a0c200-9163-11ea-885a-59d6793a9ff8.png)

---

## Comment 6

> Username: eldiener  
> Created_at: 2020-05-08 19:09:30 UTC  
> Url: https://github.com/boostorg/regex/pull/105#issuecomment-625971474  

>   
>   
> ![screen2](https://user-images.githubusercontent.com/5011768/81437826-b3a5d180-9163-11ea-8d0f-f677362702e5.png)  
> ![screen1](https://user-images.githubusercontent.com/5011768/81437838-b6a0c200-9163-11ea-885a-59d6793a9ff8.png)  
  
Got it ! Thanks !

---

## Comment 7

> Username: eldiener  
> Created_at: 2020-05-09 01:28:38 UTC  
> Url: https://github.com/boostorg/regex/pull/105#issuecomment-626083450  

Can this please be merged ?

---

## Comment 8

> Username: eldiener  
> Created_at: 2020-05-18 19:28:17 UTC  
> Url: https://github.com/boostorg/regex/pull/105#issuecomment-630390383  

Is there any reason that this can not be merged ? There are no conflicts and all tests pass.

---

## Comment 9

> Username: eldiener  
> Created_at: 2020-06-17 18:10:58 UTC  
> Url: https://github.com/boostorg/regex/pull/105#issuecomment-645536244  

Can this please be merged ?

---
