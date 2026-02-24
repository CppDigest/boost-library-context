# #106 - Warning when using boost.range [Closed]

> Username: madmongo1  
> Created at: 2020-06-23 17:04:35 UTC  
> Updated at: 2020-09-16 14:37:44 UTC  
> Closed at: 2020-09-16 14:37:43 UTC  
> Url: https://github.com/boostorg/range/issues/106  

to reproduce:  
  
check out latest develop of boost source tree (currently scheduled for 1.74 release)  
  
```bash  
$ ./bootstrap.sh  
$ ./b2 toolset=clang cxxstd=latest variant=release -j 12 libs/beast//example libs/beast//test  
```  
  
example warning:  
```  
In file included from libs/beast/example/http/server/coro/http_server_coro.cpp:20:  
In file included from ./boost/asio/spawn.hpp:19:  
In file included from ./boost/coroutine/all.hpp:11:  
In file included from ./boost/coroutine/coroutine.hpp:10:  
In file included from ./boost/coroutine/asymmetric_coroutine.hpp:17:  
In file included from ./boost/range.hpp:19:  
In file included from ./boost/range/metafunctions.hpp:20:  
./boost/range/result_iterator.hpp:20:1: warning: This header is deprecated. Use <boost/range/iterator.hpp> instead. [-W#pragma-messages]  
BOOST_HEADER_DEPRECATED("<boost/range/iterator.hpp>")  
^  
```

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-07-28 01:06:37 UTC  
> Url: https://github.com/boostorg/range/issues/106#issuecomment-664716883  

The ODEInt library hits this as well. I assume `range.hpp` is a convenience header; perhaps it can be updated to use the right (non-deprecated) headers.

---

## Comment 2

> Username: jefftrull  
> Created at: 2020-07-28 01:15:40 UTC  
> Url: https://github.com/boostorg/range/issues/106#issuecomment-664719646  

The headers `const_reverse_iterator.hpp` and `reverse_result_iterator.hpp` are also getting included, with a deprecation warning.

---

## Comment 3

> Username: jefftrull  
> Created at: 2020-07-28 02:42:22 UTC  
> Url: https://github.com/boostorg/range/issues/106#issuecomment-664742669  

It's [in metafunctions.hpp](https://github.com/boostorg/range/blob/72e2a4457a13eba0b8cddd2e68f058f74bd3d890/include/boost/range/metafunctions.hpp#L20)

---

## Comment 4

> Username: dvhwgumby  
> Created at: 2020-09-12 06:41:37 UTC  
> Url: https://github.com/boostorg/range/issues/106#issuecomment-691426750  

The fix to this is trivial, and included below.  Let me know if you'd prefer a pull request, but this is literally a three line fix.  The correct headers are already being included, so the three obsolete ones need merely be removed.  
```  
diff -c metafunctions.hpp.orig metafunctions.hpp  
*** metafunctions.hpp.orig	2020-09-11 23:38:54.000000000 -0700  
--- metafunctions.hpp	2020-09-11 23:39:37.000000000 -0700  
***************  
*** 17,26 ****  
    
  #include <boost/range/iterator.hpp>  
  #include <boost/range/has_range_iterator.hpp>  
- #include <boost/range/result_iterator.hpp>  
  #include <boost/range/reverse_iterator.hpp>  
- #include <boost/range/const_reverse_iterator.hpp>  
- #include <boost/range/reverse_result_iterator.hpp>  
  #include <boost/range/value_type.hpp>  
  #include <boost/range/size_type.hpp>  
  #include <boost/range/difference_type.hpp>  
--- 17,23 ----  
```

---

## Comment 5

> Username: glenfe  
> Created at: 2020-09-16 14:37:43 UTC  
> Url: https://github.com/boostorg/range/issues/106#issuecomment-693449599  

Didn't realize there was an open issue for this or I would have closed this when I fixed it in 486c74fbeba9ee9975e6d68e974230ec8ec22fb0. Closing...
