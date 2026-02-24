# #685 refactor: Replace deprecated libtiff v4.3 typedefs with C99 fixed-size integers [Merged]

> Username: marco-langer  
> Created at: 2022-06-09 18:17:55 UTC  
> Updated at: 2022-06-25 22:51:25 UTC  
> Merged at: 2022-06-25 22:51:24 UTC  
> Closed at: 2022-06-25 22:51:24 UTC  
> Url: https://github.com/boostorg/gil/pull/685  

### Description  
  
This PR replaces libtiff's fixed-size typedef `uint32` with C99's fixed-size typedef `std::uint32_t`.  
  
Rational: libtiff's typedefs are deprecated since v4.3.0 and we already see this deprecation warning in our CI for builds with clang on macos-10.15.  
  
C99's fixed-size typedefs and libtiffs typedefs are almost interchangeable, with the notable exception for 64 bit macOS and a breaking change between long and long long according to [this](https://gitlab.com/libtiff/libtiff/-/merge_requests/185) and [this](https://gitlab.com/libtiff/libtiff/-/merge_requests/205) discussion. Currently I don't think this will affect Gil as we only used `uint32` from libtiffs typedefs, but nevertheless it might be a good idea to check the impact of this PR on macOS builds before merging it into develop.  
  
- [ ] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-09 19:21:32 UTC  
> Updated_at: 2022-06-25 21:22:47 UTC  
> Url: https://github.com/boostorg/gil/pull/685#issuecomment-1151526714  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/685?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#685](https://codecov.io/gh/boostorg/gil/pull/685?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c1026cb) into [develop](https://codecov.io/gh/boostorg/gil/commit/526c8983364a68cb5222fc54c193b1cbbe541a12?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (526c898) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #685   +/-   ##  
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
> Created_at: 2022-06-09 19:39:43 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/685#pullrequestreview-1001889327  

Awesome, thanks!

---

## Comment 3

> Username: striezel  
> Created_at: 2022-06-13 18:04:24 UTC  
> Url: https://github.com/boostorg/gil/pull/685#issuecomment-1154221350  

> C99's fixed-size typedefs and libtiffs typedefs are almost interchangeable, with the notable exception for 64 bit macOS and a breaking change between long and long long according to [this](https://gitlab.com/libtiff/libtiff/-/merge_requests/185) and [this](https://gitlab.com/libtiff/libtiff/-/merge_requests/205) discussion. Currently I don't think this will affect Gil as we only used `uint32` from libtiffs typedefs, but nevertheless it might be a good idea to check the impact of this PR on macOS builds before merging it into develop.  
  
I don't have much of a clue about macOS in general, but FWIW GitHub Actions is now also offering macOS 12 runners: https://github.blog/changelog/2022-06-13-github-actions-macos-12-for-github-hosted-runners-is-now-generally-available/ Maybe that can be helpful here to extend tests to newer macOS versions.

---

## Review 4 [Approved]

> Username: mloskot  
> Created_at: 2022-06-25 22:51:15 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/685#pullrequestreview-1019357600  

All CI's are now green. Thanks again!

---
