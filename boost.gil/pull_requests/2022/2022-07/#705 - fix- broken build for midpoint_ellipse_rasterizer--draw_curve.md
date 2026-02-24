# #705 fix: broken build for midpoint_ellipse_rasterizer::draw_curve [Merged]

> Username: marco-langer  
> Created at: 2022-07-02 14:07:46 UTC  
> Updated at: 2022-07-04 19:09:22 UTC  
> Merged at: 2022-07-04 19:09:21 UTC  
> Closed at: 2022-07-04 19:09:21 UTC  
> Url: https://github.com/boostorg/gil/pull/705  

### Description  
  
This PR fixes a bug in `midpoint_ellipse_rasterizer::draw_curve()`, [which I have introduced in my other PR](https://github.com/boostorg/gil/pull/695) while working on the `apply_rasterizer` function. I have also added missing test cases to cover these invocations.  
  
### Tasklist  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-07-02 14:22:11 UTC  
> Updated_at: 2022-07-02 16:38:33 UTC  
> Url: https://github.com/boostorg/gil/pull/705#issuecomment-1172906719  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/705?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#705](https://codecov.io/gh/boostorg/gil/pull/705?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ce61114) into [develop](https://codecov.io/gh/boostorg/gil/commit/233dc53ff7888647f0ae39af70aac39e575d1e9b?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (233dc53) will **increase** coverage by `0.13%`.  
> The diff coverage is `100.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #705      +/-   ##  
===========================================  
+ Coverage    80.97%   81.10%   +0.13%       
===========================================  
  Files          116      117       +1       
  Lines         5130     5171      +41       
===========================================  
+ Hits          4154     4194      +40       
- Misses         976      977       +1       
```

---

## Review 2 [Commented]

> Username: mloskot  
> Created_at: 2022-07-02 14:37:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/705#pullrequestreview-1026763611  

📁 test/extension/rasterization/line.cpp

```diff
  24 |+ // operator<< overload is needed to use points directly with boost test macros
  25 |+ namespace boost { namespace gil {
  26 |+ std::ostream& operator<<(std::ostream& os, point_t const& p)
```

> Username: mloskot  
> Created_at: 2022-07-02 14:37:40 UTC  
> Url: https://github.com/boostorg/gil/pull/705#discussion_r912368986  

You could follow our convention of `test_fixture.hpp`-s, create `test/core/point/test_fixture.hpp` and put this operator in there


---
