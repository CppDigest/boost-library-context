# #28 Add missing includes, switch to min_category, add iterators namespace [Open]

> Username: Lastique  
> Created at: 2025-02-08 15:25:22 UTC  
> Updated at: 2025-02-08 15:28:10 UTC  
> Url: https://github.com/boostorg/tokenizer/pull/28  

Add missing includes for Boost.Iterator and standard library components. Specifically, `enable_if_convertible` in Boost.Iterator is now defined in its own header.  
  
Use `min_category` instead of `minimum_category` as the latter is deprecated. The replacement is a direct equivalent, but doesn't bring the dependency on Boost.MPL and is variadic.  
  
Explicitly specify `iterators` namespace for Boost.Iterator components.

---

## Comment 1

> Username: Lastique  
> Created_at: 2025-02-08 15:28:09 UTC  
> Url: https://github.com/boostorg/tokenizer/pull/28#issuecomment-2645783975  

Note: C++03 CI jobs will fail as expected, as C++03 is no longer supported.

---
