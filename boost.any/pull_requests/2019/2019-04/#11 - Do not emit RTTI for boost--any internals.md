# #11 Do not emit RTTI for boost::any internals [Closed]

> Username: apolukhin  
> Created at: 2019-04-26 20:46:54 UTC  
> Updated at: 2021-04-27 06:57:55 UTC  
> Closed at: 2019-06-22 09:13:39 UTC  
> Url: https://github.com/boostorg/any/pull/11  



---

## Comment 1

> Username: apolukhin  
> Created_at: 2019-06-22 09:13:39 UTC  
> Url: https://github.com/boostorg/any/pull/11#issuecomment-504647736  

The approach from this PR gave no advantages. Codegen and the binary size did not change.  
  
Closed in favour of https://github.com/boostorg/any/pull/12  
Some of the small improvements were merged in https://github.com/boostorg/any/commit/8e24fabe52b414b8931994e2e51c65f90ab85f99

---
