# #892 - 'hash_value': identifier not found [Closed]

> Username: oliverportcnc  
> Created at: 2024-12-19 14:29:31 UTC  
> Updated at: 2024-12-20 03:07:17 UTC  
> Closed at: 2024-12-20 03:07:17 UTC  
> Url: https://github.com/boostorg/url/issues/892  

This error occurs both in MSVC and in GCC, just with slightly different error messages.  
  
See [https://github.com/boostorg/boost/issues/991](https://github.com/boostorg/boost/issues/991) for more details.  
  
This can be fixed by adding  
  
`#include <boost/container_hash/hash.hpp>`  
  
inside `string_view_base.hpp`.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2024-12-19 14:52:32 UTC  
> Url: https://github.com/boostorg/url/issues/892#issuecomment-2554429641  

Mmm... Then Boost.URL would depend on ContainerHash.  
  
The problem here is that you're using a Boost release where `hash_value` had already been removed from `core::string_view` but hasn't been removed yet from Boost.URL.  
  
The problem should have been fixed already since https://github.com/boostorg/url/commit/428ea43f40790b84c59b0792fe2e8abccdf2f558 has been merged in `master`.

---

## Comment 2

> Username: oliverportcnc  
> Created at: 2024-12-19 14:58:17 UTC  
> Url: https://github.com/boostorg/url/issues/892#issuecomment-2554460185  

Thanks. This issue is still somehow present in Boost master though as shown in the linked ticket?

---

## Comment 3

> Username: alandefreitas  
> Created at: 2024-12-19 20:03:41 UTC  
> Url: https://github.com/boostorg/url/issues/892#issuecomment-2555676352  

Have you tested this more recently? The issue is from 3 days ago and the develop branch has been merged to master yesterday.

---

## Comment 4

> Username: seektechnz  
> Created at: 2024-12-20 00:08:49 UTC  
> Updated at: 2024-12-20 00:20:21 UTC  
> Url: https://github.com/boostorg/url/issues/892#issuecomment-2555990274  

Seems to be working fine now, thanks.
