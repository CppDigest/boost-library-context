# #5 Remove dependency on deprecated type_traits headers. [Merged]

> Username: eldiener  
> Created at: 2015-03-30 05:51:59 UTC  
> Updated at: 2015-04-02 14:36:10 UTC  
> Merged at: 2015-04-02 14:36:10 UTC  
> Closed at: 2015-04-02 14:36:10 UTC  
> Url: https://github.com/boostorg/function/pull/5  

This change in Boost function headers removes the dependencies on the deprecated ice_xxx.hpp headers in type_traits.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-03-30 15:30:29 UTC  
> Url: https://github.com/boostorg/function/pull/5#issuecomment-87723537  

This looks very straightforward to me; anyone else have a comment?

---

## Comment 2

> Username: eldiener  
> Created_at: 2015-03-30 15:48:18 UTC  
> Updated_at: 2015-03-30 15:48:52 UTC  
> Url: https://github.com/boostorg/function/pull/5#issuecomment-87730220  

I did not need to include boost/mpl/logical.hpp as including boost/mpl/not.hpp is enough. I realized it after generating the pull request.

---

## Comment 3

> Username: eldiener  
> Created_at: 2015-04-02 11:19:12 UTC  
> Url: https://github.com/boostorg/function/pull/5#issuecomment-88868437  

I updated the change to remove the unnecessary include of boost/mpl/logical.hpp. I tested it and it is fine.

---
