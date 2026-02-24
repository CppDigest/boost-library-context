# #581 fix: link statically against boost-python on linux [Merged]

> Username: joriscarrier  
> Created at: 2020-04-25 10:02:33 UTC  
> Updated at: 2021-10-02 21:01:50 UTC  
> Merged at: 2020-06-10 22:52:11 UTC  
> Closed at: 2020-06-10 22:52:12 UTC  
> Url: https://github.com/boostorg/build/pull/581  

closed #580

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-05-12 12:40:11 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/581#pullrequestreview-409997883  

📁 src/tools/python.jam

```diff
  54 |- lib pthread ;
  54 |+ lib pthread :
  55 |+     : <conditional>@pthread_linking
```

> Username: grafikrobot  
> Created_at: 2020-05-12 12:40:04 UTC  
> Updated_at: 2020-05-12 19:30:27 UTC  
> Url: https://github.com/boostorg/build/pull/581#discussion_r423699789  

This all looks equivalent to adding `<target-os>linux:<link>shared` instead of using a full conditional rule. Also, does this apply to other systems that use pthread? I.e. should pthread always be shared link?

> Username: joriscarrier  
> Created_at: 2020-05-12 19:34:23 UTC  
> Url: https://github.com/boostorg/build/pull/581#discussion_r423983765  

no problem with non-linux systems, pthread can be link statically


---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:49 UTC  
> Url: https://github.com/boostorg/build/pull/581#issuecomment-932819966  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
