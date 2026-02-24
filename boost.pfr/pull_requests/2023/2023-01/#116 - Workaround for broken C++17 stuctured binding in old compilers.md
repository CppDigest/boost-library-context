# #116 Workaround for broken C++17 stuctured binding in old compilers [Closed]

> Username: denzor200  
> Created at: 2023-01-04 10:59:40 UTC  
> Updated at: 2023-01-09 17:33:07 UTC  
> Closed at: 2023-01-09 17:33:03 UTC  
> Url: https://github.com/boostorg/pfr/pull/116  

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
> Created_at: 2023-01-04 11:12:10 UTC  
> Updated_at: 2023-01-07 03:47:01 UTC  
> Url: https://github.com/boostorg/pfr/pull/116#issuecomment-1370791583  

## Pull Request Test Coverage Report for [Build 3860270847](https://coveralls.io/builds/55726587)  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/55726587/badge)](https://coveralls.io/builds/55726587) |  
| :-- | --: |  
| Change from base [Build 3807146163](https://coveralls.io/builds/55534705): |  0.0% |  
| Covered Lines: | 356 |  
| Relevant Lines: | 356 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Comment 2

> Username: denzor200  
> Created_at: 2023-01-09 17:33:03 UTC  
> Url: https://github.com/boostorg/pfr/pull/116#issuecomment-1375998736  

Actual PR here: https://github.com/boostorg/pfr/pull/119

---
