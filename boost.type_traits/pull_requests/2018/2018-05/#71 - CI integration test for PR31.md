# #71 CI integration test for PR31. [Merged]

> Username: jzmaddock  
> Created at: 2018-05-08 18:01:13 UTC  
> Updated at: 2019-11-02 11:38:57 UTC  
> Merged at: 2018-05-12 07:18:50 UTC  
> Closed at: 2018-05-12 07:18:50 UTC  
> Url: https://github.com/boostorg/type_traits/pull/71  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-05-10 07:55:15 UTC  
> Url: https://github.com/boostorg/type_traits/pull/71#issuecomment-387981093  

@csernib : this is the integration test for you PR - it works an absolute treat for gcc and msvc, but fails with every version of clang tested :(  
  
See the travis tests above for examples.  
  
Any ideas?

---

## Comment 2

> Username: csernib  
> Created_at: 2018-05-26 21:28:22 UTC  
> Url: https://github.com/boostorg/type_traits/pull/71#issuecomment-392289198  

It's a bit late, but I believe Clang was right to reject the original 7f792f9 version, as the cast expression in the default template argument could be instantiated using just the arguments of the class template, thus causing a hard error instead of SFINAE. Simply making the typecast depend on some extra template parameters of `test` could have fixed it too:  
```  
template<class F, class T, unsigned = sizeof((T*)((F*)(0)))> static YES test(int*);  
template<class F, class T> static NO test(...);  
static const bool value = sizeof(test<From, To>(nullptr)) == sizeof(YES);  
```

---
