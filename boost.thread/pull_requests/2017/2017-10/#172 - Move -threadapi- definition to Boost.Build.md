# #172 Move <threadapi> definition to Boost.Build [Merged]

> Username: karzhenkov  
> Created at: 2017-10-07 15:08:45 UTC  
> Updated at: 2017-10-08 14:59:27 UTC  
> Merged at: 2017-10-07 15:34:35 UTC  
> Closed at: 2017-10-07 15:34:35 UTC  
> Url: https://github.com/boostorg/thread/pull/172  

This is last step of moving `<threadapi>` and its auto-detection rule to `Boost.Build`.  
Previous PRs are boostorg/build#251 and boostorg/boost#166.

---

## Comment 1

> Username: viboes  
> Created_at: 2017-10-07 15:26:48 UTC  
> Url: https://github.com/boostorg/thread/pull/172#issuecomment-334943205  

But that this mean that the interface has changed now and that the user needs to use `threadapi-feature`?

---

## Review 2 [Commented]

> Username: viboes  
> Created_at: 2017-10-07 15:29:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/172#pullrequestreview-67835652  

📁 build/Jamfile.v2

```diff
 152 |         local api = [ $(property-set).get <threadapi> ] ;
```

> Username: viboes  
> Created_at: 2017-10-07 15:29:30 UTC  
> Url: https://github.com/boostorg/thread/pull/172#discussion_r143333452  

Why this is not changed?  
Is to preserve the use of threadapi?


---

## Comment 3

> Username: karzhenkov  
> Created_at: 2017-10-07 15:29:46 UTC  
> Url: https://github.com/boostorg/thread/pull/172#issuecomment-334943415  

The module `threadapi-feature.jam` is new, so there is no problem. The name of feature it defines is still `<threadapi>`.

---

## Comment 4

> Username: viboes  
> Created_at: 2017-10-07 15:42:22 UTC  
> Url: https://github.com/boostorg/thread/pull/172#issuecomment-334944297  

Ok.  
  
It is not working yet.  
  
https://ci.appveyor.com/project/viboes/thread/build/1.0.134-develop/job/tj7us66lyvxkbctl

---

## Comment 5

> Username: viboes  
> Created_at: 2017-10-07 15:44:25 UTC  
> Url: https://github.com/boostorg/thread/pull/172#issuecomment-334944447  

I've applied the patches locally and the build works on my macos.  
However the test doesn't work.  
  
There are some changes on test/Jamfile.v2 that I have not.

---

## Comment 6

> Username: viboes  
> Created_at: 2017-10-07 15:49:09 UTC  
> Url: https://github.com/boostorg/thread/pull/172#issuecomment-334944749  

It is better after doing the cleanup as described here test/Jamfile.v2  
https://github.com/boostorg/thread/commit/f67e6d44171c30df0819ab62238b0a18941baea1#diff-e97299ad55a4013dc19889043aa464a5

---

## Comment 7

> Username: karzhenkov  
> Created_at: 2017-10-07 16:11:31 UTC  
> Url: https://github.com/boostorg/thread/pull/172#issuecomment-334946308  

test/Jamfile.v2 is the same in boostorg/thread@f67e6d4 and boostorg/thread@b128586, isn't it?  
  
Did you add `<conditional>` to `Jamroot`?

---

## Comment 8

> Username: viboes  
> Created_at: 2017-10-07 16:55:28 UTC  
> Updated_at: 2017-10-07 16:56:19 UTC  
> Url: https://github.com/boostorg/thread/pull/172#issuecomment-334949797  

Yes, sorry, I didn't read correctly.  
  
And yes, I did it locally.

---

## Comment 9

> Username: karzhenkov  
> Created_at: 2017-10-07 19:22:16 UTC  
> Url: https://github.com/boostorg/thread/pull/172#issuecomment-334959871  

I can't reproduce the error you got. With clean checkout of top-level `develop` branch (boostorg/boost@3f32a98) test is started normally. There is no message "error: No best alternative for libs/thread/build/thread_sources". Could you check again?

---

## Comment 10

> Username: viboes  
> Created_at: 2017-10-08 07:03:07 UTC  
> Url: https://github.com/boostorg/thread/pull/172#issuecomment-334987618  

I said "It is better after doing the cleanup as described here test/Jamfile.v2"

---

## Comment 11

> Username: karzhenkov  
> Created_at: 2017-10-08 07:55:15 UTC  
> Updated_at: 2017-10-08 08:00:40 UTC  
> Url: https://github.com/boostorg/thread/pull/172#issuecomment-334989858  

`test/Jamfile.v2` in `develop` is reverted to state it had before #160. The [cleanup](https://github.com/boostorg/thread/commit/f67e6d44171c30df0819ab62238b0a18941baea1#diff-e97299ad55a4013dc19889043aa464a5) you point to does actually this rollback.  
  
Did I miss something?  
Is there still the test error?

---

## Comment 12

> Username: viboes  
> Created_at: 2017-10-08 14:02:45 UTC  
> Url: https://github.com/boostorg/thread/pull/172#issuecomment-335008694  

Everything is ok :)  
  
Thanks for working on this.

---
