# #10 Fix expected nested-name-specifier before ‘const’ error [Merged]

> Username: oxidase  
> Created at: 2017-12-31 10:18:25 UTC  
> Updated at: 2017-12-31 11:30:25 UTC  
> Merged at: 2017-12-31 11:29:29 UTC  
> Closed at: 2017-12-31 11:29:29 UTC  
> Url: https://github.com/boostorg/heap/pull/10  

2b45bfef7f722b729a29419caa2c47be445a3e79 introduces a regression:  
```  
/home/miha/foss/boost/boost/heap/detail/stable_heap.hpp:581:22: error: expected nested-name-specifier before ‘const’  
     typedef typename const Alloc::value_type& const_reference;  
                      ^~~~~  
/home/miha/foss/boost/boost/heap/detail/stable_heap.hpp:581:35: error: invalid use of qualified-name ‘Alloc::value_type’  
     typedef typename const Alloc::value_type& const_reference;  
                                   ^~~~~~~~~~  
```  
  
PR flips the const qualifier to the right side.

---
