# #73 - Has move ctor/assign intrinsics are absent on any version of clang [Closed]

> Username: Flast  
> Created at: 2018-05-17 12:55:35 UTC  
> Updated at: 2018-05-19 19:47:34 UTC  
> Closed at: 2018-05-19 19:47:34 UTC  
> Url: https://github.com/boostorg/type_traits/issues/73  

`__has_feature` and `__has_builtin` on clang don't help to detect move ctor/assing checking intrinsic, see https://wandbox.org/permlink/PagEil6PmRSgjELM , thus `boost::has_trivial_move_*` don't work...  
  
Both of the intrinsics are introduced on clang 3.3 release, so I'm trying to detect those via other feature.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-05-17 17:27:30 UTC  
> Url: https://github.com/boostorg/type_traits/issues/73#issuecomment-389945701  

Testing a fix in https://github.com/boostorg/type_traits/pull/74

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-05-19 19:47:34 UTC  
> Url: https://github.com/boostorg/type_traits/issues/73#issuecomment-390428199  

This should be addressed in develop now.
