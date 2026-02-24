# #340 Add tests for extension/numeric/channel_numeric_operations.hpp [Merged]

> Username: mloskot  
> Created at: 2019-07-19 10:51:44 UTC  
> Updated at: 2019-07-25 07:18:55 UTC  
> Merged at: 2019-07-25 07:18:52 UTC  
> Closed at: 2019-07-25 07:18:52 UTC  
> Url: https://github.com/boostorg/gil/pull/340  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
  
Add static assertions to verify channel types are convertible to expected result type.  
  
Add new and update existing Doxygen comments describing interfaces in `extension/numeric/channel_numeric_operations.hpp`.  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-07-22 19:15:49 UTC  
> Url: https://github.com/boostorg/gil/pull/340#issuecomment-513917049  

@stefanseefeld This one is needed too, as I'm having more tests for Numeric in the pipeline. Unless you have a strong objection, I'd like to merge it.  
  
Generally, I believe new tests should have not broken anything and are safe to merge, so a thorough reviews do not seem necessary. Tests improvements and updates are continuously ongoing maintenance anyway.

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-07-25 07:18:44 UTC  
> Url: https://github.com/boostorg/gil/pull/340#issuecomment-514927958  

I need these tests in, so merging now.  
I'll address any feedback it arrives, in future.

---
