# #170 - Incorrect tuple_size if type contains native array member [Open]

> Username: wingfiring  
> Created at: 2024-05-06 03:38:54 UTC  
> Updated at: 2024-10-17 09:04:08 UTC  
> Url: https://github.com/boostorg/pfr/issues/170  

Reproduce:  
```  
struct Foo { int x[2]; };  
```  
gcc: 13.2.0  
compile command: ` g++ -std=c++20  a.cpp -Ipath/to/boost`  
  
Expect:  
`expects(boost::pfr::tuple_size_v<Foo> == 1);`  
Actual:  
`boost::pfr::tuple_size_v<Foo> == 2`  
  
Workaround(might be problematic, but works for me):  
```patch  
diff --git a/include/boost/pfr/detail/fields_count.hpp b/include/boost/pfr/detail/fields_count.hpp  
index 8d1354e..04bb89c 100644  
--- a/include/boost/pfr/detail/fields_count.hpp  
+++ b/include/boost/pfr/detail/fields_count.hpp  
@@ -150,11 +150,11 @@ constexpr void* assert_first_not_base(std::index_sequence<>) noexcept  
 ///////////////////// Helper for SFINAE on fields count  
 template <class T, std::size_t... I, class /*Enable*/ = typename std::enable_if<std::is_copy_constructible<T>::value>::type>  
 constexpr auto enable_if_constructible_helper(std::index_sequence<I...>) noexcept  
-    -> typename std::add_pointer<decltype(T{ ubiq_lref_constructor{I}... })>::type;  
+    -> typename std::add_pointer<decltype(T{ {ubiq_lref_constructor{I}}... })>::type;  
   
 template <class T, std::size_t... I, class /*Enable*/ = typename std::enable_if<!std::is_copy_constructible<T>::value>::type>  
 constexpr auto enable_if_constructible_helper(std::index_sequence<I...>) noexcept  
-    -> typename std::add_pointer<decltype(T{ ubiq_rref_constructor{I}... })>::type;  
+    -> typename std::add_pointer<decltype(T{ {ubiq_rref_constructor{I}}... })>::type;  
   
 template <class T, std::size_t N, class /*Enable*/ = decltype( enable_if_constructible_helper<T>(detail::make_index_sequence<N>()) ) >  
 using enable_if_constructible_helper_t = std::size_t;  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-10-17 09:04:05 UTC  
> Url: https://github.com/boostorg/pfr/issues/170#issuecomment-2418974158  

The patch breaks multiple tests. It requires some additional tweaking, I'd appreciate a PR.
