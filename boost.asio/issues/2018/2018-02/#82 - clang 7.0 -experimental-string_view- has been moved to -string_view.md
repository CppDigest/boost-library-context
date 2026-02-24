# #82 - clang 7.0 <experimental/string_view> has been moved to <string_view> [Closed]

> Username: rwols  
> Created at: 2018-02-11 12:46:00 UTC  
> Updated at: 2020-12-30 00:48:42 UTC  
> Closed at: 2020-12-30 00:48:26 UTC  
> Url: https://github.com/boostorg/asio/issues/82  

I've been trying out clang 7.0 and it seems they moved experimenta/string_view to string_view:  
  
```  
In file included from /usr/local/include/boost/asio/ip/address.hpp:21:  
In file included from /usr/local/include/boost/asio/detail/string_view.hpp:23:  
/usr/local/include/c++/v1/experimental/string_view:11:2: error: "<experimental/string_view> has been removed. Use <string_view> instead."  
#error "<experimental/string_view> has been removed. Use <string_view> instead."  
```  
  
This quick fix in `boost/asio/detail/config.hpp` fixes my issue, but I'm not sure how it interplays with AppleClang.  
  
```diff  
--- config.hpp	2018-02-11 13:41:39.000000000 +0100  
+++ config.hpp.mine	2018-02-11 13:41:21.000000000 +0100  
@@ -775,7 +775,11 @@  
 #   if (__cplusplus >= 201402)  
 #    if __has_include(<experimental/string_view>)  
 #     define BOOST_ASIO_HAS_STD_STRING_VIEW 1  
-#     define BOOST_ASIO_HAS_STD_EXPERIMENTAL_STRING_VIEW 1  
+#     if __clang_major__ >= 7  
+#      undef BOOST_ASIO_HAS_STD_EXPERIMENTAL_STRING_VIEW  
+#     else  
+#      define BOOST_ASIO_HAS_STD_EXPERIMENTAL_STRING_VIEW 1  
+#     endif // __clang_major__ >= 7  
 #    endif // __has_include(<experimental/string_view>)  
 #   endif // (__cplusplus >= 201402)  
 #  endif // defined(__clang__)  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2018-03-19 16:34:15 UTC  
> Url: https://github.com/boostorg/asio/issues/82#issuecomment-374277052  

Wouldn't it be better to check for `__has_include(<string_view>)` and use that over `<experimental/string_view>`?

---

## Comment 2

> Username: knejadfard  
> Created at: 2018-03-30 14:27:33 UTC  
> Url: https://github.com/boostorg/asio/issues/82#issuecomment-377535701  

Confirming that pull request #91 fixes the issue for me.  
My libcxx revision: 4e177b9  
Boost version: 1.66.0

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 00:48:08 UTC  
> Url: https://github.com/boostorg/asio/issues/82#issuecomment-752288891  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#597](https://github.com/chriskohlhoff/asio/issues/597).
