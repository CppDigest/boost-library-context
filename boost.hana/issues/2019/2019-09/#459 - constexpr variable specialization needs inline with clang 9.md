# #459 - constexpr variable specialization needs inline with clang 9 [Closed]

> Username: mgaunard  
> Created at: 2019-09-03 10:14:19 UTC  
> Updated at: 2019-09-03 19:48:48 UTC  
> Closed at: 2019-09-03 19:48:48 UTC  
> Url: https://github.com/boostorg/hana/issues/459  

I get multiple definitions of curry_or_call<0>

---

## Comment 1

> Username: ricejasonf  
> Created at: 2019-09-03 18:18:25 UTC  
> Url: https://github.com/boostorg/hana/issues/459#issuecomment-527577591  

This has a fix in the development branch: https://github.com/boostorg/hana/commit/7218a046c731b2548b5ae3db55fb711ccda8fbc4

---

## Comment 2

> Username: mgaunard  
> Created at: 2019-09-03 19:48:48 UTC  
> Url: https://github.com/boostorg/hana/issues/459#issuecomment-527611147  

sorry I didn't catch that this was fixed by using an anonymous namespace since 1.71.
