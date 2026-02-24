# #951 fix: define get_distance_measure by default [Merged]

> Username: barendgehrels  
> Created at: 2021-12-15 13:05:10 UTC  
> Updated at: 2022-01-12 09:24:34 UTC  
> Merged at: 2021-12-15 15:43:58 UTC  
> Closed at: 2021-12-15 15:43:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/951  

This fixes circleci  
https://app.circleci.com/pipelines/github/boostorg/geometry/257/workflows/00234789-5f80-4b23-9075-4c359ec83d11/jobs/15405

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2021-12-15 15:44:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/951#issuecomment-994915000  

Merged to get ci green

---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2021-12-15 16:36:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/951#pullrequestreview-832948130  

📁 include/boost/geometry/algorithms/detail/overlay/get_distance_measure.hpp

```diff
  70 |+                              Point const& )
  71 |+     {
  72 |+         const result_type result;
```

> Username: vissarion  
> Created_at: 2021-12-15 15:07:35 UTC  
> Updated_at: 2021-12-15 16:36:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/951#discussion_r769715520  

should be `result_type const`, right?


---

## Comment 3

> Username: vissarion  
> Created_at: 2021-12-15 16:37:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/951#issuecomment-994963229  

Sorry, I was a bit late, I am OK in any case.

---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2021-12-15 18:22:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/951#pullrequestreview-833182353  

📁 include/boost/geometry/algorithms/detail/overlay/get_distance_measure.hpp

```diff
  62 | template <typename CalculationType, typename CsTag>
  63 | struct get_distance_measure
  64 |-         : not_implemented<CsTag>
```

> Username: awulkiew  
> Created_at: 2021-12-15 18:22:31 UTC  
> Updated_at: 2021-12-15 18:22:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/951#discussion_r769880100  

This struct is now going to be instantiated for all geometries defining `CSTag` other than `cartesian_tag`, also for `cs_undefined_tag` regardless of the strategy passed by the user. This is incorrect.


---

## Comment 5

> Username: awulkiew  
> Created_at: 2021-12-15 18:23:42 UTC  
> Updated_at: 2021-12-15 18:35:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/951#issuecomment-995050610  

Are we abandoning the commit message format? I'm ok with it. What is this format exactly? Prefixing with `fix:` and `feature:` or something else too?

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2022-01-12 09:24:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/951#issuecomment-1010827877  

> Are we abandoning the commit message format? I'm ok with it. What is this format exactly? Prefixing with `fix:` and `feature:` or something else too?  
  
Sorry, I see this only now. Now, I by accident used a different format from another project.   
Usually I use `[subject] Message` for example `[test] Fixed expectations` or `[buffer] Implemented for geographic` or so.

---
