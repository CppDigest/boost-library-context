# #48 The `boost/pending/integer_log2.hpp` is deprecated [Closed]

> Username: Kojoley  
> Created at: 2018-10-24 22:45:10 UTC  
> Updated at: 2018-11-01 00:28:54 UTC  
> Closed at: 2018-11-01 00:28:50 UTC  
> Url: https://github.com/boostorg/random/pull/48  

The `boost/pending/integer_log2.hpp` was deprecated for a long time and not it generates a warning https://github.com/boostorg/integer/pull/13.  
  
```  
In file included from range_run.cpp:11:  
In file included from ../../../../boost/random.hpp:36:  
In file included from ../../../../boost/random/additive_combine.hpp:27:  
In file included from ../../../../boost/random/linear_congruential.hpp:30:  
In file included from ../../../../boost/random/detail/const_mod.hpp:23:  
In file included from ../../../../boost/random/detail/large_arithmetic.hpp:19:  
In file included from ../../../../boost/random/detail/integer_log2.hpp:19:  
../../../../boost/pending/integer_log2.hpp:7:1: warning: This header is deprecated. Use <boost/integer/integer_log2.hpp> instead. [-W#pragma-messages]  
BOOST_HEADER_DEPRECATED("<boost/integer/integer_log2.hpp>");  
^  
../../../../boost/config/header_deprecated.hpp:23:37: note: expanded from macro 'BOOST_HEADER_DEPRECATED'  
# define BOOST_HEADER_DEPRECATED(a) BOOST_PRAGMA_MESSAGE("This header is deprecated. Use " a " instead.")  
                                    ^  
../../../../boost/config/pragma_message.hpp:24:34: note: expanded from macro 'BOOST_PRAGMA_MESSAGE'  
# define BOOST_PRAGMA_MESSAGE(x) _Pragma(BOOST_STRINGIZE(message(x)))  
                                 ^  
<scratch space>:51:2: note: expanded from here  
 message("This header is deprecated. Use " "<boost/integer/integer_log2.hpp>" " instead.")  
 ^  
1 warning generated.  
```

---

## Comment 1

> Username: NAThompson  
> Created_at: 2018-10-24 23:41:21 UTC  
> Url: https://github.com/boostorg/random/pull/48#issuecomment-432864479  

Isn’t this the same as PR35?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-10-24 23:45:46 UTC  
> Url: https://github.com/boostorg/random/pull/48#issuecomment-432865220  

It is. I have not spotted that. At least I made some noise here and I hope the things will happen soon because I am having a build error now.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2018-10-25 15:40:44 UTC  
> Url: https://github.com/boostorg/random/pull/48#issuecomment-433101405  

@swatanabe : It looks like you have insufficient time for maintenance of this library. Could you add me to the maintainers and I will help Kojoley out?

---
