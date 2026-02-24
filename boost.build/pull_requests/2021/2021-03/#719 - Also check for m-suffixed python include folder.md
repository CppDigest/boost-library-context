# #719 Also check for m-suffixed python include folder [Closed]

> Username: Flamefire  
> Created at: 2021-03-30 13:39:43 UTC  
> Updated at: 2021-10-02 20:59:36 UTC  
> Closed at: 2021-05-25 03:29:08 UTC  
> Url: https://github.com/boostorg/build/pull/719  

* src/tools/python.jam  
    (compute-default-paths): When the python include folder without any  
    suffix does not exist, but one with a m-suffix does, use the latter  
  
Cherry-picked the patch from upstream

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2021-05-25 03:29:08 UTC  
> Url: https://github.com/boostorg/build/pull/719#issuecomment-847501530  

https://github.com/bfgroup/b2/commit/ca7d1729b160da2f07be5369349dc00b1b8313ef#diff-2d60e472a93312a30ddcfa658afb3e8a07c005127d2f98cbdf08f47ff7d8eebe

---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:35 UTC  
> Url: https://github.com/boostorg/build/pull/719#issuecomment-932819671  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
