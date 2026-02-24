# #24 Implemented segments_type operations [Closed]

> Username: zajo  
> Created at: 2021-01-27 00:53:38 UTC  
> Updated at: 2021-09-14 04:54:46 UTC  
> Closed at: 2021-09-14 04:54:46 UTC  
> Url: https://github.com/boostorg/url/pull/24  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2021-01-27 01:46:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/24#pullrequestreview-576914863  

📁 include/boost/url/impl/url_base.hpp

```diff
  54 |         segments_type::value_type;
  55 | 
  56 |+     /// A pointer to an element
```

> Username: vinniefalco  
> Created_at: 2021-01-27 01:46:33 UTC  
> Url: https://github.com/boostorg/url/pull/24#discussion_r564966049  

hmm... header files with public interfaces should not be in impl/ or detail/.


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-09-14 04:54:46 UTC  
> Url: https://github.com/boostorg/url/pull/24#issuecomment-918800861  

This code has been refactored and no longer resembles the previous version

---
