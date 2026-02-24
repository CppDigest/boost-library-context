# #1314 [ci] Simplify the documentation CI [Merged]

> Username: vissarion  
> Created at: 2024-09-27 09:21:47 UTC  
> Updated at: 2024-09-30 19:55:09 UTC  
> Merged at: 2024-09-30 09:03:43 UTC  
> Closed at: 2024-09-30 09:03:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1314  

Following the suggestion [here](https://github.com/boostorg/geometry/pull/1312#issuecomment-2373918114) this is a simplification for the documentation CI script.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-09-28 07:22:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1314#pullrequestreview-2334961038  

📁 .github/workflows/documentation.yml

```diff
  99 |         cd libs/geometry/doc
 105 |-         python ./make_qbk.py
 100 |+         $BOOST_ROOT/b2
```

> Username: barendgehrels  
> Created_at: 2024-09-28 07:22:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1314#discussion_r1779434895  

This is perfect! So we call `b2` now instead of `make_qbk.py`, cool.  
  
Can we then remove the fact that `make_qbk.py` calls `b2`?   
Is everyone following this workflow?  
Or do we, if we do that, disturb the making of the official release documentation?

> Username: vissarion  
> Created_at: 2024-09-30 09:16:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1314#discussion_r1780729319  

I do not know what scripts/workflow is used to build the official release documentation. @barendgehrels do you know?

> Username: barendgehrels  
> Created_at: 2024-09-30 19:55:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1314#discussion_r1781665051  

No, I don't know that exactly...


---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2024-09-28 07:22:42 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1314#pullrequestreview-2334961076  

👍

---
