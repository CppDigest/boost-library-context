# #1215 Remove deprecated test-suite rule [Closed]

> Username: FantasqueX  
> Created at: 2023-11-19 15:04:15 UTC  
> Updated at: 2024-05-21 15:22:14 UTC  
> Closed at: 2024-05-21 15:22:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1215  

According to  
https://github.com/bfgroup/b2/commit/597350653ada0f192dbe4b6d6ee509429f1cda45, b2 deprecated test-suite rule in 2007.

---

## Review 1 [Changes requested]

> Username: vissarion  
> Created_at: 2023-12-01 13:00:10 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/1215#pullrequestreview-1759767194  

Thanks for this PR!   
`test-suite` is deprecated indeed but it should be replaced by an equivalent rule i.e. `alias` instead of removing it.   
  
If you removed it then the user would no longer be able to run commands of the form   
`b2 test/algorithms/area//boost-geometry-algorithms-area`

---

## Comment 2

> Username: FantasqueX  
> Created_at: 2023-12-03 12:16:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1215#issuecomment-1837463362  

Thanks for the review. The reason why I remove it instead of replacing with `alias` is that users can do the same thing using `b2 test/algorithms/area`. There is only one alias target in one directory.  
  
Well, it's not that important. If you still think `alias` should be used, I'll change the PR.

---

## Comment 3

> Username: vissarion  
> Created_at: 2023-12-04 10:40:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1215#issuecomment-1838273163  

I agree that there is a workaround for calling all tests in a directory but it seems like a braking change. Also I am not sure that the commands in the circleCI script will be run after this change. Cf. https://github.com/boostorg/geometry/blob/develop/.circleci/config.yml#L77C74-L77C116

---

## Comment 4

> Username: FantasqueX  
> Created_at: 2023-12-04 16:51:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1215#issuecomment-1839055663  

Sorry for my carelessness. It is a breaking change at least for CI.

---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2024-03-27 15:19:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1215#pullrequestreview-1963670985  

📁 test/algorithms/Jamfile

```diff
  40 |+ run is_valid.cpp                 : : : <define>BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE : algorithms_is_valid_alternative ;
  41 |+ run is_valid_failure.cpp         : : : <define>BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE : algorithms_is_valid_failure_alternative ;
  42 |+ run is_valid_geo.cpp             : : : <define>BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE : algorithms_is_valid_geo_alternative ;
```

> Username: barendgehrels  
> Created_at: 2024-03-27 15:19:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1215#discussion_r1541326110  

In the meantime, this is gone and should be omitted.  
What are the plans with this PR?


---
