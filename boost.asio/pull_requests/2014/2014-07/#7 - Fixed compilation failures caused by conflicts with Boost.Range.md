# #7 Fixed compilation failures caused by conflicts with Boost.Range. [Closed]

> Username: Lastique  
> Created at: 2014-07-29 07:24:11 UTC  
> Updated at: 2014-07-31 05:54:28 UTC  
> Closed at: 2014-07-31 05:54:28 UTC  
> Url: https://github.com/boostorg/asio/pull/7  

Added names to some enum types to avoid compilation failures with gcc 4.4 caused by operators defined by Boost.Range in namespace boost. The operators fail to compile because they attempt to instantiate a template with the anonymous enum type in its parameters.  
  
Additionally, added explicit type casts to the enum values to avoid any further possible ambiguities.

---

## Comment 1

> Username: Lastique  
> Created_at: 2014-07-31 05:54:28 UTC  
> Url: https://github.com/boostorg/asio/pull/7#issuecomment-50715582  

The problem is resolved with https://github.com/ericniebler/range/commit/c2a7ac41971d461230c79a0e83ffe547390c8e6e so this pull request is no longer necessary.

---
