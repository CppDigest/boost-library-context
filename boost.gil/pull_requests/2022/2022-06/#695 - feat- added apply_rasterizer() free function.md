# #695 feat: added apply_rasterizer() free function [Merged]

> Username: marco-langer  
> Created at: 2022-06-26 15:01:33 UTC  
> Updated at: 2022-07-01 20:40:52 UTC  
> Merged at: 2022-06-27 07:54:58 UTC  
> Closed at: 2022-06-27 07:54:59 UTC  
> Url: https://github.com/boostorg/gil/pull/695  

### Description  
  
This PR implements the `gil::apply_rasterizer()` free function mentioned in #680.  
  
However, I had to change the API of all three rasterizers quite considerably in order to squeeze them through this common interface. I am not sure if all those API changes are still debatable if we consider the deadline for Boost 1.80 in three days on 29th of June.  
  
If you look at the updated examples, the usability is improved considerable (in my opinion) and it is still worth it (in my opinion) to discuss the PR, but I also understand that it might be too late for these kind of major API changes.  
  
  
### References  
  
#680  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-26 15:17:06 UTC  
> Url: https://github.com/boostorg/gil/pull/695#issuecomment-1166562013  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/695?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#695](https://codecov.io/gh/boostorg/gil/pull/695?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0fd05dc) into [develop](https://codecov.io/gh/boostorg/gil/commit/adddbec8961a152db5751802307cc4ee977fd15d?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (adddbec) will **increase** coverage by `0.05%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #695      +/-   ##  
===========================================  
+ Coverage    80.69%   80.75%   +0.05%       
===========================================  
  Files          116      116                
  Lines         5072     5086      +14       
===========================================  
+ Hits          4093     4107      +14       
  Misses         979      979                
```

---

## Comment 2

> Username: mloskot  
> Created_at: 2022-06-27 07:52:59 UTC  
> Url: https://github.com/boostorg/gil/pull/695#issuecomment-1167008459  

> I am not sure if all those API changes are still debatable if we consider the deadline for Boost 1.80 in three days on 29th of June.  
  
The rasterizers have not been released yet, so we are not breaking anything, IMO.

---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2022-06-27 07:53:55 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/695#pullrequestreview-1019758591  

Thanks!

---

## Review 4 [Commented]

> Username: mloskot  
> Created_at: 2022-07-01 17:34:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/695#pullrequestreview-1026486044  

📁 include/boost/gil/extension/rasterization/ellipse.hpp

```diff
 185 |     {
 177 |-         draw_curve(view, pixel, center, obtain_trajectory(semi_axes));
 186 |+         draw_curve(view, obtain_trajectory());
```

> Username: mloskot  
> Created_at: 2022-07-01 17:34:29 UTC  
> Url: https://github.com/boostorg/gil/pull/695#discussion_r912141592  

@marco-langer AFAICT, this `draw_curve(view, obtain_trajectory());` invocation is not correct as the `draw_curve` still takes 3 arguments: `view`, `pixel` and `trajectory_points`.  
  
The CI jobs for this PR were green though. It looks like this part is not covered by tests.   
It will fail during compilation of `example/rasterizer_ellipse.cpp` though.

> Username: marco-langer  
> Created_at: 2022-07-01 18:01:47 UTC  
> Url: https://github.com/boostorg/gil/pull/695#discussion_r912158779  

I will fix it and also add test cases

> Username: mloskot  
> Created_at: 2022-07-01 20:40:52 UTC  
> Url: https://github.com/boostorg/gil/pull/695#discussion_r912239912  

Thank you!


---
