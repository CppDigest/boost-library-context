# #8 Remove dependency on boost::bind [Merged]

> Username: Burgos  
> Created at: 2018-11-02 16:01:25 UTC  
> Updated at: 2018-11-02 23:19:48 UTC  
> Merged at: 2018-11-02 20:05:26 UTC  
> Closed at: 2018-11-02 20:05:26 UTC  
> Url: https://github.com/boostorg/proto/pull/8  

Since it was reported [1] that using boost::bind slows down compiling  
Boost.Spirit and that makes placeholders ambiguous, the entire  
issue is avoided just by removing boost::bind dependency and passing  
copyable functor to fusion::for_each.  
  
[1] https://github.com/boostorg/proto/pull/7#issuecomment-435413109

---

## Comment 1

> Username: Burgos  
> Created_at: 2018-11-02 16:03:44 UTC  
> Url: https://github.com/boostorg/proto/pull/8#issuecomment-435429063  

cc @preejackie

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-11-02 23:00:38 UTC  
> Url: https://github.com/boostorg/proto/pull/8#issuecomment-435533466  

I should have checked this...  
```  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/spirit/test/x3/x3_binary.test/clang-linux-5.0/release/cxxstd-14-iso/stdlib-libc++/threading-multi/visibility-hidden/binary.o  
In file included from binary.cpp:9:  
In file included from ../../../../boost/spirit/include/support_argument.hpp:16:  
In file included from ../../../../boost/spirit/home/support/argument.hpp:18:  
In file included from ../../../../boost/spirit/include/phoenix_core.hpp:11:  
In file included from ../../../../boost/phoenix/core.hpp:13:  
In file included from ../../../../boost/phoenix/core/debug.hpp:17:  
In file included from ../../../../boost/proto/proto.hpp:13:  
../../../../boost/proto/debug.hpp:158:40: error: 'boost::proto::detail::display_expr_impl_functor' is an incomplete type  
                fusion::for_each(expr, display_expr_impl_functor(display));  
                                       ^  
../../../../boost/proto/debug.hpp:118:16: note: forward declaration of 'boost::proto::detail::display_expr_impl_functor'  
        struct display_expr_impl_functor;  
               ^  
1 error generated.  
```

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-11-02 23:10:15 UTC  
> Url: https://github.com/boostorg/proto/pull/8#issuecomment-435535143  

At least this revealed to me that `boost/proto/proto.hpp` includes `iostream`.

---

## Comment 4

> Username: Burgos  
> Created_at: 2018-11-02 23:19:48 UTC  
> Url: https://github.com/boostorg/proto/pull/8#issuecomment-435536674  

Sigh.. Sorry, I should have checked this with more than one compiler (I've tested with the only one that I have access to), or there should be a CI in order :(. It's a simple fix however, but I hope you can test it on your side: https://github.com/boostorg/proto/pull/9  
  
 It's after midnight here, so I'm going to bed now :-(.

---
