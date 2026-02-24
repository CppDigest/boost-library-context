# #701 feat: Generalized threshold algorithm for all kind of pixel types [Open]

> Username: marco-langer  
> Created at: 2022-06-27 18:56:17 UTC  
> Updated at: 2025-09-05 14:06:01 UTC  
> Url: https://github.com/boostorg/gil/pull/701  

### Description  
  
This PR implements the channel traits min and max values for binary, truncate and adaptive thresholds and thereby extends the possible pixel types for these algorithms.  
  
The current test cases are all green, but I will also add new test cases for floating point types and signed integer types soon.  
  
### References  
  
Closes #697  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-27 19:08:15 UTC  
> Updated_at: 2025-09-05 14:06:01 UTC  
> Url: https://github.com/boostorg/gil/pull/701#issuecomment-1167768717  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/701?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 80.94%. Comparing base ([`4dbf35a`](https://app.codecov.io/gh/boostorg/gil/commit/4dbf35a510fd2bad2ed8c59c4dd523de4b3cf77b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`f37758d`](https://app.codecov.io/gh/boostorg/gil/commit/f37758dc43c6b4e08d6f2a1d2b8280a15477621c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 81 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #701   +/-   ##  
========================================  
  Coverage    80.93%   80.94%             
========================================  
  Files          116      116             
  Lines         5130     5132    +2       
========================================  
+ Hits          4152     4154    +2       
  Misses         978      978             
```  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
