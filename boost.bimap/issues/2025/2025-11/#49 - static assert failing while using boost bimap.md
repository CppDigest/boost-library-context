# #49 - static assert failing while using boost bimap [Closed]

> Username: sloriot  
> Created at: 2025-11-04 14:54:03 UTC  
> Updated at: 2025-11-06 08:20:36 UTC  
> Closed at: 2025-11-05 20:22:58 UTC  
> Url: https://github.com/boostorg/bimap/issues/49  

I recently got the following assertion in our testsuite (several versions of gcc recent and older):  
  
```  
**/usr/local/include/boost/multi_index/detail/node_type.hpp:41:3: error: static assertion failed: detail::is_index_list<IndexSpecifierList>::value  
   41 |   BOOST_STATIC_ASSERT(detail::is_index_list<IndexSpecifierList>::value);  
**  
```  
  
The file was recently modified in 890f0329390fac7ef7b9607f5c9dd1ef51a6779b so I was wondering it you already saw this error?  
  
In the meantime I'll try to prepare a self-contained example.

---

## Comment 1

> Username: sloriot  
> Created at: 2025-11-04 14:58:08 UTC  
> Url: https://github.com/boostorg/bimap/issues/49#issuecomment-3487651466  

Actually the following example from the distrib produces the error:  
https://www.boost.org/doc/libs/latest/libs/bimap/example/standard_map_comparison.cpp

---

## Comment 2

> Username: joaquintides  
> Created at: 2025-11-04 17:25:07 UTC  
> Updated at: 2025-11-04 19:21:28 UTC  
> Url: https://github.com/boostorg/bimap/issues/49#issuecomment-3487651472  

Hi Sebastien, what version of Boost are you using? Commit https://github.com/boostorg/multi_index/commit/890f0329390fac7ef7b9607f5c9dd1ef51a6779b has not been shipped yet (will be in Boost 1.91, spring 2026), and the example mentioned seems to be working ok with Boost 1.89:  
  
https://godbolt.org/z/EoKWq93nc

---

## Comment 3

> Username: sloriot  
> Created at: 2025-11-04 19:00:22 UTC  
> Updated at: 2025-11-04 19:02:44 UTC  
> Url: https://github.com/boostorg/bimap/issues/49#issuecomment-3487651490  

Hi Joaquín, the testsuite with the issue is using the HEAD of all boost packages.

---

## Comment 4

> Username: joaquintides  
> Created at: 2025-11-04 19:12:19 UTC  
> Url: https://github.com/boostorg/bimap/issues/49#issuecomment-3487651502  

Understood. So, yes, this problem stems from the breaking change in MultiIndex you mention. Working right now in   
  
https://github.com/boostorg/bimap/tree/fix/compatibility-multi_index-1.91

---

## Comment 5

> Username: sloriot  
> Created at: 2025-11-06 08:20:36 UTC  
> Url: https://github.com/boostorg/bimap/issues/49#issuecomment-3495789847  

Thanks!
