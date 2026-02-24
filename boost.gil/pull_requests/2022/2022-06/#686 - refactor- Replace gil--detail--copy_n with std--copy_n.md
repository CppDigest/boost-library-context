# #686 refactor: Replace gil::detail::copy_n with std::copy_n [Open]

> Username: marco-langer  
> Created at: 2022-06-11 07:44:52 UTC  
> Updated at: 2025-09-06 03:13:02 UTC  
> Url: https://github.com/boostorg/gil/pull/686  

### Description  
  
This PR replaces Gil's `gil::detail::copy_n` with `std::copy_n`.  
  
### Tasklist  
  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-11 07:51:23 UTC  
> Updated_at: 2025-09-06 03:13:02 UTC  
> Url: https://github.com/boostorg/gil/pull/686#issuecomment-1152876925  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/686?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `85.71429%` with `1 line` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 80.27%. Comparing base ([`151fd9c`](https://app.codecov.io/gh/boostorg/gil/commit/151fd9c8a318a4249e244eab959592143422a04d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`46497d0`](https://app.codecov.io/gh/boostorg/gil/commit/46497d096597ff35bc8e9e334e15c207072404b8?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 96 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #686      +/-   ##  
===========================================  
- Coverage    80.32%   80.27%   -0.06%       
===========================================  
  Files          117      117                
  Lines         5032     5019      -13       
===========================================  
- Hits          4042     4029      -13       
  Misses         990      990                
```  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Review 2 [Commented]

> Username: mloskot  
> Created_at: 2022-06-12 13:08:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/686#pullrequestreview-1003611144  

📁 include/boost/gil/algorithm.hpp

```diff
 243 |             diff_t numToCopy=(n<l ? n : l);
 244 |-             detail::copy_n(src.x(), numToCopy, dst.x());
 244 |+             std::copy_n(src.x(), numToCopy, dst.x());
```

> Username: mloskot  
> Created_at: 2022-06-12 13:08:26 UTC  
> Url: https://github.com/boostorg/gil/pull/686#discussion_r895172244  

> Added line #L244 was not covered by tests  
  
Hmm, this one is interesting. I'd have expected those to be fully covered.

> Username: marco-langer  
> Created_at: 2022-06-12 16:39:08 UTC  
> Updated_at: 2022-06-12 16:45:06 UTC  
> Url: https://github.com/boostorg/gil/pull/686#discussion_r895204940  

I don't see any test cases for `core\algorithm\copy_pixels`. The other specializations of `struct copier_n` are apparently triggered by some unrelated test cases from extension or image processing tests AFAIS.  
  
Edit: this is not true, there are some legacy tests, which do not cover this case. However, the Jamfile in `test\legacy` says these tests should not be refactored or extended. Thus, should we rather add new `copy_pixels` tests in `core\algorithm\copy_pixels.cpp`?


---
