# #95 Fix bad static_cast in resume_with: [Merged]

> Username: djarek  
> Created at: 2018-12-16 03:06:39 UTC  
> Updated at: 2018-12-16 18:13:18 UTC  
> Merged at: 2018-12-16 13:46:27 UTC  
> Closed at: 2018-12-16 13:46:27 UTC  
> Url: https://github.com/boostorg/context/pull/95  

- fix a cast from `void*` to the wrong dynamic type. The code cast from  
  `tuple<decay_t<Fn>>*` -> `void*` -> `tuple<Fn>*`, which worked when an rvalue  
  was passed to resume_with(), but broke if the function was passed  
  by lvalue-reference, because it resulted in a cast from `Fn` to `Fn&`.  
- remove use of `std::tuple` - no need to instantiate it for every callable.

---

## Comment 1

> Username: olk  
> Created_at: 2018-12-16 13:46:33 UTC  
> Url: https://github.com/boostorg/context/pull/95#issuecomment-447644675  

ty

---
