# #91 Add the version of find_backward() that is actaully going in to the s… [Closed]

> Username: tzlaine  
> Created at: 2022-05-04 19:52:55 UTC  
> Updated at: 2022-05-06 14:32:38 UTC  
> Closed at: 2022-05-06 14:32:38 UTC  
> Url: https://github.com/boostorg/algorithm/pull/91  

…tandard -- find_last().

---

## Review 1 [Commented]

> Username: glenfe  
> Created_at: 2022-05-05 21:27:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/91#pullrequestreview-963970989  

📁 include/boost/algorithm/find_last.hpp

```diff
 199 |+     {
 200 |+         return algorithm::find_last_if_not(
 201 |+             std::begin(r), std::end(r), pred, proj);
```

> Username: glenfe  
> Created_at: 2022-05-05 21:23:10 UTC  
> Updated_at: 2022-05-05 21:27:22 UTC  
> Url: https://github.com/boostorg/algorithm/pull/91#discussion_r866325050  

Shouldn't this call begin and end unqualified?   
  
```  
using std::begin;  
using std::end;  
return algorithm::find_last_if_not(begin(r), end(r), pred, proj);  
```

---

📁 include/boost/algorithm/find_last.hpp

```diff
 118 |+         find_last(R && r, const T & value, Proj proj = {})
 119 |+     {
 120 |+         return algorithm::find_last(std::begin(r), std::end(r), value, proj);
```

> Username: glenfe  
> Created_at: 2022-05-05 21:26:24 UTC  
> Updated_at: 2022-05-05 21:27:22 UTC  
> Url: https://github.com/boostorg/algorithm/pull/91#discussion_r866327092  

Same question here regarding ADL for `begin` and `end`.


📁 include/boost/algorithm/cxx20_helper.hpp

```diff
  49 |+         template<typename R>
  50 |+         BOOST_CXX14_CONSTEXPR explicit subrange(const R & r) :
  51 |+             first_(std::begin(r)), last_(std::end(r))
```

> Username: glenfe  
> Created_at: 2022-05-05 21:26:59 UTC  
> Updated_at: 2022-05-05 21:27:22 UTC  
> Url: https://github.com/boostorg/algorithm/pull/91#discussion_r866327421  

Same question here about ADL for `begin` and `end`


---

## Comment 2

> Username: tzlaine  
> Created_at: 2022-05-06 14:32:35 UTC  
> Url: https://github.com/boostorg/algorithm/pull/91#issuecomment-1119689078  

Good call.  I'll make another PR with the fix.

---
