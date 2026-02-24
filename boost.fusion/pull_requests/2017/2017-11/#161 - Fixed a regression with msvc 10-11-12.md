# #161 Fixed a regression with msvc 10/11/12 [Merged]

> Username: Flast  
> Created at: 2017-11-23 14:11:07 UTC  
> Updated at: 2017-11-25 00:54:52 UTC  
> Merged at: 2017-11-24 22:23:18 UTC  
> Closed at: 2017-11-24 22:23:18 UTC  
> Url: https://github.com/boostorg/fusion/pull/161  

I'm not sure why they are crashed with original one...  
  
close #159

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2017-11-23 14:20:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/fusion/pull/161#pullrequestreview-78730914  

📁 test/sequence/github-159.cpp

```diff
  13 |+     boost::fusion::vector<int, float> v1;
  14 |+     boost::fusion::vector<int, float> v2(v1);
  15 |+     v1 = v2;
```

> Username: Kojoley  
> Created_at: 2017-11-23 14:20:59 UTC  
> Url: https://github.com/boostorg/fusion/pull/161#discussion_r152811725  

Why `test/sequence/vector_copy.cpp` did not catch this?

> Username: Flast  
> Created_at: 2017-11-23 14:28:13 UTC  
> Updated_at: 2017-11-23 14:28:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/161#discussion_r152813372  

IMO, `vector_copy.cpp` is fusion specific feature test for copying, but this issue caused by combination with `<boost/type.hpp>`, so it's not a fusion specific.


---

## Comment 2

> Username: Flast  
> Created_at: 2017-11-24 00:47:13 UTC  
> Url: https://github.com/boostorg/fusion/pull/161#issuecomment-346713812  

Anything else?

---

## Review 3 [Approved]

> Username: Kojoley  
> Created_at: 2017-11-24 13:09:16 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/fusion/pull/161#pullrequestreview-78910570  

I can confirm the Spirit tests are fixed with this. Thanks @Flast!

---

## Comment 4

> Username: djowel  
> Created_at: 2017-11-24 22:23:11 UTC  
> Url: https://github.com/boostorg/fusion/pull/161#issuecomment-346902104  

Wonderful!

---

## Comment 5

> Username: Flast  
> Created_at: 2017-11-25 00:54:44 UTC  
> Url: https://github.com/boostorg/fusion/pull/161#issuecomment-346910136  

Thank you for your confirmation! I'll ask release manager to merge this patch into master.

---
