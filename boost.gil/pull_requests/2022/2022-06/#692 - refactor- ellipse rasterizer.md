# #692 refactor: ellipse rasterizer [Merged]

> Username: marco-langer  
> Created at: 2022-06-26 08:54:32 UTC  
> Updated at: 2022-06-26 16:58:54 UTC  
> Merged at: 2022-06-26 11:33:54 UTC  
> Closed at: 2022-06-26 11:33:54 UTC  
> Url: https://github.com/boostorg/gil/pull/692  

### Description  
  
This PR implements some of the mentioned issues in [my other comment](https://github.com/boostorg/gil/pull/585#issuecomment-1144412220) regarding the ellipse rasterizer, namely:  
  
- renamed `midpoint_elliptical_rasterizer` to `midpoint_ellipse_rasterizer`  
- replaced colour vector with pixel type  
- replaced std::array with point type for center and semi-axis  
- removed unnecessary deep copies of temporaries.  
- removed printing warnings to cout  
  
There are still some open questions, e.g. why circle/line and ellipse rasterizer have different signedness for point types and why they implement the call operator differently. However, I am not sure if we are able to find any consensus on these topics before the deadline for Boost 1.80.  
  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-26 09:21:13 UTC  
> Url: https://github.com/boostorg/gil/pull/692#issuecomment-1166465989  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/692?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#692](https://codecov.io/gh/boostorg/gil/pull/692?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7279ffa) into [develop](https://codecov.io/gh/boostorg/gil/commit/151fd9c8a318a4249e244eab959592143422a04d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (151fd9c) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #692   +/-   ##  
========================================  
  Coverage    80.32%   80.32%             
========================================  
  Files          117      117             
  Lines         5032     5032             
========================================  
  Hits          4042     4042             
  Misses         990      990             
```

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2022-06-26 11:33:21 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/692#pullrequestreview-1019410737  

Thank you very much!  
  
Since @meshtag reacted to your questions in https://github.com/boostorg/gil/pull/585#issuecomment-1144445685 and he did not express any objections, I'm happy to approve it and merge it for Boost 1.80

---

## Review 3 [Commented]

> Username: meshtag  
> Created_at: 2022-06-26 16:58:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/692#pullrequestreview-1019457655  

LGTM!  
Thanks a lot @marco-langer and @mloskot !

---
