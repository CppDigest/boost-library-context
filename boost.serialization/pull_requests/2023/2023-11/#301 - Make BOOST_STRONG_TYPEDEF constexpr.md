# #301 Make BOOST_STRONG_TYPEDEF constexpr [Open]

> Username: jakubmiernik  
> Created at: 2023-11-24 22:03:52 UTC  
> Updated at: 2024-01-09 05:54:09 UTC  
> Url: https://github.com/boostorg/serialization/pull/301  

Lack of constexpr constructor for BOOST_STRONG_TYPEDEF  cause errors in clang and warning in gcc when using in constexpr functions.

---

## Comment 1

> Username: robertramey  
> Created_at: 2024-01-09 05:54:08 UTC  
> Url: https://github.com/boostorg/serialization/pull/301#issuecomment-1882453556  

Suppose we make this change.  What happens when we use it with a compiler which does not support constant expressions?  I think this needs to be more elaborate to be totally portable.  I see merit in the idea, but I don't see it being portable to all compilers that the boost serialization library supports.  
  
FYI. The serialization library no longer uses this macro.  It's been left in there as it seems that many find it useful.

---
