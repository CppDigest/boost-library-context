# #527 Add inverse function for affine matrix3x2 [Merged]

> Username: sdebionne  
> Created at: 2020-11-04 10:27:52 UTC  
> Updated at: 2022-05-04 06:50:24 UTC  
> Merged at: 2021-01-20 22:09:58 UTC  
> Closed at: 2021-01-20 22:09:58 UTC  
> Url: https://github.com/boostorg/gil/pull/527  

### Description  
  
Adds `inverse(boost::gil::matrix3x2<T> m)` that returns the inverse of the given affine matrix to the `numeric` extension. This is useful to implement an undo of an affine transformation or to compute the coordinate of points in the original coordinate system gievn the coordinate in transformed one.   
  
Use `BOOST_TEST_WITH` and defines `with_tolerance` rather `BOOST_GIL_TEST_IS_CLOSE` as discussed in boostorg/core#38  
  
### Tasklist  
  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Commented]

> Username: mloskot  
> Created_at: 2020-11-04 16:38:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/527#pullrequestreview-523538260  

📁 include/boost/gil/extension/numeric/affine.hpp

```diff
  96 |+ boost::gil::matrix3x2<T> inverse(boost::gil::matrix3x2<T> m)
  97 |+ {
  98 |+     T determinant = m.a * m.d - m.b * m.c;
```

> Username: mloskot  
> Created_at: 2020-11-04 16:38:03 UTC  
> Updated_at: 2020-11-05 10:34:10 UTC  
> Url: https://github.com/boostorg/gil/pull/527#discussion_r517478124  

Nitpick: I'd generally stick `T const determinant`, especially in bodies of longer functions.


---

## Review 2 [Commented]

> Username: mloskot  
> Created_at: 2020-11-04 16:38:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/527#pullrequestreview-523538960  

📁 test/extension/numeric/matrix3x2.cpp

```diff
  19 |- // FIXME: Remove when https://github.com/boostorg/core/issues/38 happens
  20 |- #define BOOST_GIL_TEST_IS_CLOSE(a, b, epsilon) BOOST_TEST_LT(std::fabs((a) - (b)), (epsilon))
  19 |+ // Tolerance predicate for floating point comparison to use with BOOST_TEST_WITH
```

> Username: mloskot  
> Created_at: 2020-11-04 16:38:50 UTC  
> Updated_at: 2020-11-05 10:34:10 UTC  
> Url: https://github.com/boostorg/gil/pull/527#discussion_r517478642  

Sweet, thanks for this!

> Username: sdebionne  
> Created_at: 2020-11-05 10:39:14 UTC  
> Url: https://github.com/boostorg/gil/pull/527#discussion_r517952175  

A basic sets of predicate should come with Boost.Core Lightweight Test IMHO...

> Username: mloskot  
> Created_at: 2020-11-05 16:07:24 UTC  
> Updated_at: 2020-11-05 16:07:25 UTC  
> Url: https://github.com/boostorg/gil/pull/527#discussion_r518169808  

Yes, it would be useful


---

## Comment 3

> Username: sdebionne  
> Created_at: 2020-11-10 09:21:22 UTC  
> Url: https://github.com/boostorg/gil/pull/527#issuecomment-724575320  

> No output has been received in the last 10m0s, this potentially indicates a stalled build or something wrong with the build itself.  
  
@mloskot Before re-running all the pipelines because one single little tiny travis stalled job, is there a way, as maintainers, to trigger a re-build of a specific job? Just to save the polar bears (and more) from climate change...

---

## Comment 4

> Username: mloskot  
> Created_at: 2020-11-10 09:39:31 UTC  
> Updated_at: 2020-11-10 09:42:48 UTC  
> Url: https://github.com/boostorg/gil/pull/527#issuecomment-724585439  

@sdebionne Good thinking! Yes, there is a way and, I think, as a committer you are authorized to do it:  
1. Go to https://travis-ci.org/github/boostorg/gil  
2. Sign in using GitHub  
3. Hit the gray circle next to the job to re-run it  
  
![image](https://user-images.githubusercontent.com/80741/98656137-9f27d900-2340-11eb-9735-34635078b830.png)  
  
Unfortunately, this is not possible for AppVeyor, because AV does not seem to transfer the privileges from GitHub but it is owned by account used to create it, namely @stefanseefeld   
  
That is another reason I'd like to switch everything over to GitHub Actions, to streamline the CI chore as accessible across our team.

---

## Comment 5

> Username: sdebionne  
> Created_at: 2020-11-10 14:35:00 UTC  
> Url: https://github.com/boostorg/gil/pull/527#issuecomment-724740812  

Unfortunately I don't have the gray circle next to the job. Maybe some configuration on the travis side is required?  
  
> That is another reason I'd like to switch everything over to GitHub Actions, to streamline the CI chore as accessible across our team.  
  
I don't much about GitHub Actions but if it makes the CI more accessible then 👍

---

## Comment 6

> Username: sdebionne  
> Created_at: 2020-11-10 14:36:56 UTC  
> Url: https://github.com/boostorg/gil/pull/527#issuecomment-724741996  

@mloskot My bad, login out and back in fixed the permissions! Thanks for the tip.

---

## Comment 7

> Username: mloskot  
> Created_at: 2021-01-20 22:10:23 UTC  
> Updated_at: 2021-01-20 22:10:34 UTC  
> Url: https://github.com/boostorg/gil/pull/527#issuecomment-763982610  

Sorry for ridiculous delay, but I completely missed this PR. Thanks!

---
