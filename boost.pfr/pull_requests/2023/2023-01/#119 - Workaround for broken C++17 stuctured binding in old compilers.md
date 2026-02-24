# #119 Workaround for broken C++17 stuctured binding in old compilers [Merged]

> Username: denzor200  
> Created at: 2023-01-09 17:32:03 UTC  
> Updated at: 2023-01-10 17:13:44 UTC  
> Merged at: 2023-01-10 17:13:44 UTC  
> Closed at: 2023-01-10 17:13:44 UTC  
> Url: https://github.com/boostorg/pfr/pull/119  

This PR fixes compile-time errors like this:  
```  
/usr/bin/../lib/gcc/x86_64-linux-gnu/5.5.0/../../../../include/c++/5.5.0/tuple:807:33: error: implicit instantiation of undefined template 'std::tuple_size<my_struct>'  
    : integral_constant<size_t, tuple_size<_Tp>::value> { };  
                                ^  
./boost/pfr/detail/core17_generated.hpp:57:9: note: in instantiation of template class 'std::tuple_size<const my_struct>' requested here  
  auto& [a,b,c] = val; // ====================> Boost.PFR: User-provided type is not a SimpleAggregate.  
```

---

## Comment 1

> Username: coveralls  
> Created_at: 2023-01-09 17:53:23 UTC  
> Updated_at: 2023-01-09 18:39:31 UTC  
> Url: https://github.com/boostorg/pfr/pull/119#issuecomment-1376023773  

## Pull Request Test Coverage Report for [Build 3876622619](https://coveralls.io/builds/55774106)  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/55774106/badge)](https://coveralls.io/builds/55774106) |  
| :-- | --: |  
| Change from base [Build 3862777246](https://coveralls.io/builds/55730460): |  0.0% |  
| Covered Lines: | 372 |  
| Relevant Lines: | 372 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Comment 2

> Username: denzor200  
> Created_at: 2023-01-09 18:00:34 UTC  
> Url: https://github.com/boostorg/pfr/pull/119#issuecomment-1376031086  

This snippet shows that the fix works: https://godbolt.org/z/6YKh335q3

---
