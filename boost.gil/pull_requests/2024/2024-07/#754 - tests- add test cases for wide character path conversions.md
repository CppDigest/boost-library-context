# #754 tests: add test cases for wide character path conversions [Merged]

> Username: striezel  
> Created at: 2024-07-23 12:18:57 UTC  
> Updated at: 2024-08-01 07:51:49 UTC  
> Merged at: 2024-07-23 13:58:12 UTC  
> Closed at: 2024-07-23 13:58:12 UTC  
> Url: https://github.com/boostorg/gil/pull/754  

### Description  
  
Add test cases for the fixes made in #746 to avoid future regressions.  
  
_(This is the first time I created a test case for GIL, so it might not build and pass on first try.)_  
  
### References  
  
* PR #746  
* Test string is taken from #733, the issue where the error was reported.  
  
### Tasklist  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: striezel  
> Created_at: 2024-07-23 13:24:47 UTC  
> Url: https://github.com/boostorg/gil/pull/754#issuecomment-2245249845  

Tests passed on first try? :open_mouth:  
  
![Actually, I'm not even mad. That's amazing.](https://github.com/user-attachments/assets/ce72044b-faf5-46ac-bc93-0003cb9c01c9)

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2024-07-23 13:56:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/754#pullrequestreview-2194074527  

Thank you!

---

## Comment 3

> Username: striezel  
> Created_at: 2024-07-31 20:56:21 UTC  
> Url: https://github.com/boostorg/gil/pull/754#issuecomment-2261447820  

Now that I know how to use Boost's `lightweight_test` I hope to write more tests in the future. :) Especially when bugs get fixed, there should be some regression tests for the fix to avoid reintroducing a bug by accident.

---

## Comment 4

> Username: mloskot  
> Created_at: 2024-08-01 07:51:47 UTC  
> Url: https://github.com/boostorg/gil/pull/754#issuecomment-2262285768  

> there should be some regression tests for the fix to avoid reintroducing a bug by accident  
  
Indeed.  
  
We encourage PR authors to always cover features and fixes with tests, from https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#pull-requests  
> DO ensure any new features or changes to existing behaviours are covered with test cases.  
  
IMO, tests are also a very useful source of 'expected behaviour documentation', especially when the prose documentation is lacking.

---
