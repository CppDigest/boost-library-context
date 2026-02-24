# #267 [travis] Update libstdcxx for clang build jobs [Merged]

> Username: mloskot  
> Created at: 2019-03-22 17:16:48 UTC  
> Updated at: 2019-03-29 20:26:07 UTC  
> Merged at: 2019-03-22 21:15:45 UTC  
> Closed at: 2019-03-22 21:15:45 UTC  
> Url: https://github.com/boostorg/gil/pull/267  

The newer `libstdc++` versions should ensure better C++11 support.  
  
For example, help to avoid issues like this one observed during MPL to MP11 migration work:  
  
```  
error: no template named 'is_trivially_default_constructible'; did you mean 'has_trivial_default_constructor'?  
/usr/bin/../lib/gcc/x86_64-linux-gnu/4.8/../../../../include/c++/4.8/type_traits:1211:12:  
  note: 'has_trivial_default_constructor' declared here  
    struct has_trivial_default_constructor  
           ^  
```  
  
I've learned from @pdimov that _"the first really c++11 version is 4.9"_. Apparently, https://en.cppreference.com/w/cpp/compiler_support summary is not quite accurate:  
```  
Type traits | N1836 | c++11 | gcc 4.3 | clang 3.0  
```  
because N1836 is actually pre-C++11 with the old `has_*` traits.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed (waiting for boostorg/gil builds, but the branch is already green on https://travis-ci.org/mloskot/gil/builds/510003526)

---
