# #785 [strategies] [test] Fix spherical cross track and convex hull tests [Merged]

> Username: vissarion  
> Created at: 2020-12-21 13:12:52 UTC  
> Updated at: 2020-12-23 14:09:58 UTC  
> Merged at: 2020-12-23 12:49:20 UTC  
> Closed at: 2020-12-23 12:49:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/785  

This PR fixes some tests that fail after merging https://github.com/boostorg/geometry/pull/756  
See also https://app.circleci.com/pipelines/github/vissarion/geometry/58/workflows/44e84067-89f7-4938-947f-006156956840

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2020-12-22 18:27:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/785#pullrequestreview-557280946  

Looks OK but I don't know the reason to exclude convex hull tests.

📁 test/algorithms/Jamfile

```diff
  28 |-     [ run convex_hull.cpp              : : : : algorithms_convex_hull ]
  29 |-     [ run convex_hull_multi.cpp        : : : : algorithms_convex_hull_multi ]
  28 |     [ run correct.cpp                  : : : : algorithms_correct ]
```

> Username: barendgehrels  
> Created_at: 2020-12-22 18:26:30 UTC  
> Updated_at: 2020-12-22 18:27:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/785#discussion_r547435868  

So it's gone? Will it be fixed later?

> Username: vissarion  
> Created_at: 2020-12-22 18:56:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/785#discussion_r547449395  

No, they are moved in a `convex_hull` directory, see https://github.com/boostorg/geometry/pull/756/commits/eae06f70dfb2f499810564a636039ec6474c10ac

> Username: barendgehrels  
> Created_at: 2020-12-23 14:09:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/785#discussion_r547976429  

>   
>   
> No, they are moved in a `convex_hull` directory, see [eae06f7](https://github.com/boostorg/geometry/commit/eae06f70dfb2f499810564a636039ec6474c10ac)  
  
I remember now, the big PR.


---
