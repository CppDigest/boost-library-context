# #209 Use std::size if __cplusplus >= 201703L [Closed]

> Username: hazelnusse  
> Created at: 2019-05-21 23:52:33 UTC  
> Updated at: 2019-05-22 18:41:51 UTC  
> Closed at: 2019-05-22 18:41:51 UTC  
> Url: https://github.com/boostorg/math/pull/209  

Fixes #208.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-05-22 18:41:50 UTC  
> Url: https://github.com/boostorg/math/pull/209#issuecomment-494917317  

That change will break msvc which never sets __cplusplus to the correct version.  I don't really see how this fixes things for you either since unqualified calls to size() will find nothing?  
  
Whatever, closing in favour of https://github.com/boostorg/math/pull/210.

---
