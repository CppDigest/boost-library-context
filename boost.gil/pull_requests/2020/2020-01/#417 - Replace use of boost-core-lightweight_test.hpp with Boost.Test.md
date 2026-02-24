# #417 Replace use of boost/core/lightweight_test.hpp with Boost.Test [Merged]

> Username: mloskot  
> Created at: 2020-01-13 19:28:00 UTC  
> Updated at: 2020-01-13 21:38:12 UTC  
> Merged at: 2020-01-13 21:38:08 UTC  
> Closed at: 2020-01-13 21:38:08 UTC  
> Url: https://github.com/boostorg/gil/pull/417  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
  
Refactoring to unify use of the common framework across GIL tests.  
  
Additionally, added several test cases to `gil::point` tests.  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass (see https://github.com/boostorg/gil/pull/417#issuecomment-573882966)

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-01-13 21:37:38 UTC  
> Url: https://github.com/boostorg/gil/pull/417#issuecomment-573882966  

AV and AzP builds passed.  
  
Travis builds are still pending, but they passed on my private account https://travis-ci.org/mloskot/gil/builds/636524564  
  
I'm going to assume all CI pass and merge.

---
