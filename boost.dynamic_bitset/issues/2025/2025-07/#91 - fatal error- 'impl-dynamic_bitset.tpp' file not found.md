# #91 - fatal error: 'impl/dynamic_bitset.tpp' file not found [Closed]

> Username: afabri  
> Created at: 2025-07-16 10:27:12 UTC  
> Updated at: 2025-07-30 14:01:18 UTC  
> Closed at: 2025-07-29 17:11:45 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/91  

The recent commit in the file [dynamic_bitset.hpp](https://github.com/boostorg/dynamic_bitset/blob/develop/include/boost/dynamic_bitset/dynamic_bitset.hpp) where you move code in   a `.tpp`  file leads to compilation erros in a [testsuite](https://cgal.geometryfactory.com/CGAL/testsuite/CGAL-6.1-Ic-197/AABB_tree_Examples/TestReport_cgaltest_ArchLinux-clang-CXX20-Release.gz) of the CGAL library.  
  
The error  message is   
```  
In file included from /usr/local/boost/include/boost/dynamic_bitset.hpp:15:  
/usr/local/boost/include/boost/dynamic_bitset/dynamic_bitset.hpp:1420:10: fatal error: 'impl/dynamic_bitset.tpp' file not found  
 1420 | #include "impl/dynamic_bitset.tpp"  
      |          ^~~~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
````

---

## Comment 1

> Username: gennaroprota  
> Created at: 2025-07-29 17:12:39 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/91#issuecomment-3133378435  

Fixed by @sloriot. Thanks.

---

## Comment 2

> Username: gennaroprota  
> Created at: 2025-07-29 17:51:16 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/91#issuecomment-3133486024  

@afabri: Please let me know if the issue is fixed for you.

---

## Comment 3

> Username: sloriot  
> Created at: 2025-07-30 09:29:57 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/91#issuecomment-3135521622  

Hi, looks like the .tpp is actually not copied when doing make install. Can we rename it to .hpp? I guess the install is handled by another boost repo.

---

## Comment 4

> Username: gennaroprota  
> Created at: 2025-07-30 10:43:28 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/91#issuecomment-3135764837  

Hi, thanks. I've renamed it to "dynamic_bitset.ipp", which should be handled correctly. Please let me know.

---

## Comment 5

> Username: sloriot  
> Created at: 2025-07-30 11:45:07 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/91#issuecomment-3135929874  

works now locally, thanks

---

## Comment 6

> Username: gennaroprota  
> Created at: 2025-07-30 14:01:18 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/91#issuecomment-3136491544  

Thanks to you!
