# #61 - pivot.hpp std::size_t fix [Open]

> Username: urnathan  
> Created at: 2022-07-05 14:18:45 UTC  
> Updated at: 2023-01-29 00:34:21 UTC  
> Url: https://github.com/boostorg/sort/issues/61  

In applying clang implicit module machinery to boost headers I came across this problem (and by implication C++20 header-units will have the same problem).  It prevents boost/sort/common/pivot.hpp being an importable header.  
  
pivot.hpp refers to 'size_t', not std::size_t, thereby implicitly relying on its importers' contexts.  Fixed thusly, it becomes a self-contained header and so a C++ 20 importable header.  
  
```  
--- c/1.77.0/src/boost_1_77_0/boost/sort/common/pivot.hpp  
+++ w/1.77.0/src/boost_1_77_0/boost/sort/common/pivot.hpp  
@@ -14,6 +14,7 @@  
 #define __BOOST_SORT_COMMON_PIVOT_HPP  
   
 #include <cstdint>  
+#include <utility>  
   
 namespace boost  
 {  
@@ -109,7 +110,7 @@ inline Iter_t mid9 (Iter_t iter_1, Iter_t iter_2, Iter_t iter_3, Iter_t iter_4,  
 template < class Iter_t, class Compare >  
 inline void pivot9 (Iter_t first, Iter_t last, Compare comp)  
 {  
-    size_t cupo = (last - first) >> 3;  
+    std::size_t cupo = (last - first) >> 3;  
     Iter_t itaux = mid9 (first + 1, first + cupo, first + 2 * cupo,  
                          first + 3 * cupo, first + 4 * cupo, first + 5 * cupo,  
                          first + 6 * cupo, first + 7 * cupo, last - 1, comp);  
```

---

## Comment 1

> Username: fjtapia  
> Created at: 2022-07-05 19:09:01 UTC  
> Url: https://github.com/boostorg/sort/issues/61#issuecomment-1175405564  

Thanks  
I am testing the code with C++20, specially with Microsoft C++.  
At today , I am out of Spain. When return, I will upload the new Code and  
add your comment  
  
  
El mar., 5 jul. 2022 16:18, Nathan Sidwell ***@***.***>  
escribió:  
  
> In applying clang implicit module machinery to boost headers I came across  
> this problem (and by implication C++20 header-units will have the same  
> problem). It prevents boost/sort/common/pivot.hpp being an importable  
> header.  
>  
> pivot.hpp refers to 'size_t', not std::size_t, thereby implicitly relying  
> on its importers' contexts. Fixed thusly, it becomes a self-contained  
> header and so a C++ 20 importable header.  
>  
> --- c/1.77.0/src/boost_1_77_0/boost/sort/common/pivot.hpp  
> +++ w/1.77.0/src/boost_1_77_0/boost/sort/common/pivot.hpp  
> @@ -14,6 +14,7 @@  
> #define __BOOST_SORT_COMMON_PIVOT_HPP  
>  
> #include  
> +#include  
>  
> namespace boost  
> {  
> @@ -109,7 +110,7 @@ inline Iter_t mid9 (Iter_t iter_1, Iter_t iter_2,  
> Iter_t iter_3, Iter_t iter_4,  
> template < class Iter_t, class Compare >  
> inline void pivot9 (Iter_t first, Iter_t last, Compare comp)  
> {  
>  
>    - size_t cupo = (last - first) >> 3;  
>  
>  
>    - std::size_t cupo = (last - first) >> 3;  
>    Iter_t itaux = mid9 (first + 1, first + cupo, first + 2 * cupo,  
>    first + 3 * cupo, first + 4 * cupo, first + 5 * cupo,  
>    first + 6 * cupo, first + 7 * cupo, last - 1, comp);  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/61>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GHDU4JV3MXLJVIYI4DVSQ75DANCNFSM52WMOV7A>  
> .  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>

---

## Comment 2

> Username: urnathan  
> Created at: 2022-07-06 15:05:15 UTC  
> Url: https://github.com/boostorg/sort/issues/61#issuecomment-1176336709  

No rush, I've fixed the diff formatting, sorry about that.

---

## Comment 3

> Username: urnathan  
> Created at: 2022-07-07 20:36:24 UTC  
> Url: https://github.com/boostorg/sort/issues/61#issuecomment-1178196793  

oh, the utility header was 'cos of a use of std::swap, IIRC.
