# #292 - Forward declarations of std types cause compilation errors on some platforms [Closed]

> Username: Botbail  
> Created at: 2024-10-16 16:38:21 UTC  
> Updated at: 2024-10-19 21:20:25 UTC  
> Closed at: 2024-10-19 21:20:25 UTC  
> Url: https://github.com/boostorg/container/issues/292  

When compiling for a well-known console platform using C++20, the standard library implementation uses an inline namespace for versioning. This conflicts with the forward declarations in [`boost/container/detail/std_fwd.hpp`](https://github.com/boostorg/container/blob/develop/include/boost/container/detail/std_fwd.hpp) and results in names being ambiguous (e.g. `std::less` vs `std::some_inline_namespace::less`).  
  
I believe Boost is at fault here because forward declaring `std` types is not allowed; perhaps this is one reason why. Although it may hurt compile times, in order to use Boost on this platform it's necessary to include the real headers rather than forward declaring. This behaviour should be removed, or at least made optional.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-10-16 18:41:59 UTC  
> Url: https://github.com/boostorg/container/issues/292#issuecomment-2417627657  

This forward declaration is not allowed, but it's been practical for many years. I would much prefer to support your platform is possible. Can you give information about the compiler and the standard library implementation/headers. Or/and a patch? Usually inline namespaces are behind a macro that could be detected and used...

---

## Comment 2

> Username: Botbail  
> Created at: 2024-10-16 22:06:19 UTC  
> Updated at: 2024-10-17 09:22:52 UTC  
> Url: https://github.com/boostorg/container/issues/292#issuecomment-2418050510  

Edit: suggestion removed, as I found a better way which doesn't require any potentially proprietary information. See my comment below.

---

## Comment 3

> Username: Botbail  
> Created at: 2024-10-17 09:21:20 UTC  
> Updated at: 2024-10-17 09:24:12 UTC  
> Url: https://github.com/boostorg/container/issues/292#issuecomment-2419012128  

After trawling through system headers, I've found a more robust fix which should work with any environment using a Dinkumware stdlib. This involves a patch to [`boost/move/detail/std_ns_begin.hpp`](https://github.com/boostorg/move/blob/develop/include/boost/move/detail/std_ns_begin.hpp), so perhaps I should create a new issue and/or PR in that library instead? At any rate, this works:  
```diff  
#elif defined(BOOST_GNU_STDLIB) && defined(_GLIBCXX_BEGIN_NAMESPACE)  //GCC >= 4.2  
   #define BOOST_MOVE_STD_NS_BEG _GLIBCXX_BEGIN_NAMESPACE(std)  
   #define BOOST_MOVE_STD_NS_END _GLIBCXX_END_NAMESPACE  
+#elif defined(_YVALS) // Dinkumware  
+   #define BOOST_MOVE_STD_NS_BEG _STD_BEGIN  
+   #define BOOST_MOVE_STD_NS_END _STD_END  
#else  
-   #if defined(_MSC_VER) && (_MSC_VER >= 1915)  
-      #pragma warning (push)  
-      #pragma warning (disable : 4643) // Forward declaring 'X' in namespace std is not permitted by the C++ Standard  
-   #endif  
-  
   #define BOOST_MOVE_STD_NS_BEG namespace std{  
   #define BOOST_MOVE_STD_NS_END }  
#endif  
+  
+#if defined(_MSC_VER) && (_MSC_VER >= 1915)  
+   #pragma warning (push)  
+   #pragma warning (disable : 4643) // Forward declaring 'X' in namespace std is not permitted by the C++ Standard  
+#endif  
```  
  
For some reason I don't understand, for this to work on MSVC I also had to undefine these in [`std_ns_end.hpp`](https://github.com/boostorg/move/blob/develop/include/boost/move/detail/std_ns_end.hpp), which I assume is no bad thing anyway:  
```diff  
#ifdef BOOST_MOVE_STD_NS_GCC_DIAGNOSTIC_PUSH  
   #pragma GCC diagnostic pop  
   #undef BOOST_MOVE_STD_NS_GCC_DIAGNOSTIC_PUSH  
#elif defined(_MSC_VER) && (_MSC_VER >= 1915)  
   #pragma warning (pop)  
#endif   //BOOST_MOVE_STD_NS_GCC_DIAGNOSTIC_PUSH  
+  
+#undef BOOST_MOVE_STD_NS_BEG  
+#undef BOOST_MOVE_STD_NS_END  
```

---

## Comment 4

> Username: igaztanaga  
> Created at: 2024-10-19 21:20:25 UTC  
> Url: https://github.com/boostorg/container/issues/292#issuecomment-2424216160  

Many thanks for your help. I've committed the suggested patch to Boost.Move.
