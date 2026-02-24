# #106 Spelling [Merged]

> Username: jsoref  
> Created at: 2022-11-02 02:43:27 UTC  
> Updated at: 2022-11-02 21:35:23 UTC  
> Merged at: 2022-11-02 20:39:33 UTC  
> Closed at: 2022-11-02 20:39:33 UTC  
> Url: https://github.com/boostorg/optional/pull/106  

This PR corrects misspellings identified by the [check-spelling action](https://github.com/marketplace/actions/check-spelling).  
  
The misspellings have been reported at https://github.com/jsoref/boostorg-optional/commit/f7b7a1442aafd1df1658b2dd949cfcd76d5ddd94#commitcomment-88602764  
  
The action reports that the changes in this PR would make it happy: https://github.com/jsoref/boostorg-optional/commit/5042fd201bf360db409812ae56319f15108350a3  
  
Note: this PR does not include the action. If you're interested in running a spell check on every PR and push, that can be offered separately.

---

## Review 1 [Commented]

> Username: jsoref  
> Created_at: 2022-11-02 02:47:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/optional/pull/106#pullrequestreview-1164328090  

Most corrections automatically suggested by Google Sheets. All fault mine

📁 doc/28_ref_optional_semantics.qbk

```diff
 165 | 
 166 |- optional<std::unique_ptr<T>> init( std::uniqye_ptr<T>(new T(2)) );
 166 |+ optional<std::unique_ptr<T>> init( std::unique_ptr<T>(new T(2)) );
```

> Username: jsoref  
> Created_at: 2022-11-02 02:44:10 UTC  
> Updated_at: 2022-11-02 02:47:29 UTC  
> Url: https://github.com/boostorg/optional/pull/106#discussion_r1011097904  

It's possible this is intentional...


📁 include/boost/optional/detail/optional_reference_spec.hpp

```diff
 132 |     
 133 |-     // the following two implement a 'conditionally explicit' constructor: condition is a hack for buggy compilers with srewed conversion construction from const int
 133 |+     // the following two implement a 'conditionally explicit' constructor: condition is a hack for buggy compilers with screwed conversion construction from const int
```

> Username: jsoref  
> Created_at: 2022-11-02 02:46:06 UTC  
> Updated_at: 2022-11-02 02:47:29 UTC  
> Url: https://github.com/boostorg/optional/pull/106#discussion_r1011098737  

?


---

## Comment 2

> Username: akrzemi1  
> Created_at: 2022-11-02 20:39:50 UTC  
> Url: https://github.com/boostorg/optional/pull/106#issuecomment-1301203412  

Thanks for the fix.

---
