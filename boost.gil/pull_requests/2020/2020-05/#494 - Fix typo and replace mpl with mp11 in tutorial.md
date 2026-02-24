# #494 Fix typo and replace mpl with mp11 in tutorial [Merged]

> Username: codejaeger  
> Created at: 2020-05-12 15:57:42 UTC  
> Updated at: 2020-05-20 19:44:55 UTC  
> Merged at: 2020-05-20 19:44:55 UTC  
> Closed at: 2020-05-20 19:44:55 UTC  
> Url: https://github.com/boostorg/gil/pull/494  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
Updates doc at https://boostorg.github.io/gil/html/tutorial/gradient.html  
  
<!-- What does this pull request do? -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Review and approve

---

## Review 1 [Commented]

> Username: mloskot  
> Created_at: 2020-05-12 16:01:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/494#pullrequestreview-410192582  

📁 doc/tutorial/gradient.rst

```diff
 900 |-   #include <boost/mpl/vector.hpp>
 900 |+   #include <boost/mp11.hpp>
 901 |   #include <boost/gil/extension/io/jpeg_dynamic_io.hpp>
```

> Username: mloskot  
> Created_at: 2020-05-12 16:01:14 UTC  
> Updated_at: 2020-05-12 16:13:05 UTC  
> Url: https://github.com/boostorg/gil/pull/494#discussion_r423851313  

By the way, the `jpeg_dynamic_io.hpp` is not correct either and could be with `boost/gil/extension/io/jpeg/old.hpp`


---
