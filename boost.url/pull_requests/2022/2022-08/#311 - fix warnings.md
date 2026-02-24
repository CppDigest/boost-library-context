# #311 fix warnings [Closed]

> Username: alandefreitas  
> Created at: 2022-08-03 00:23:42 UTC  
> Updated at: 2022-08-03 21:05:47 UTC  
> Closed at: 2022-08-03 18:12:44 UTC  
> Url: https://github.com/boostorg/url/pull/311  

fix #291

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-03 01:20:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/311#pullrequestreview-1059592145  

📁 include/boost/url/impl/params_encoded.hpp

```diff
 163 |- params
 164 |- params_encoded::
 165 |- decoded(Allocator const& alloc) const
```

> Username: vinniefalco  
> Created_at: 2022-08-03 01:20:15 UTC  
> Url: https://github.com/boostorg/url/pull/311#discussion_r936154319  

lol


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-03 01:21:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/311#pullrequestreview-1059592646  

📁 include/boost/url/rfc/authority_rule.hpp

```diff
  42 |         @ref authority_view,
  43 |-         @ref parse.
  43 |+         @ref grammar::parse.
```

> Username: vinniefalco  
> Created_at: 2022-08-03 01:21:22 UTC  
> Url: https://github.com/boostorg/url/pull/311#discussion_r936154697  

oh yeah... `grammar::` is needed in rfc/


---
