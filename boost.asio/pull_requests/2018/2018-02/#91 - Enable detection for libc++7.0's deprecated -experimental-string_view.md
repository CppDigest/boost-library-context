# #91 Enable detection for libc++7.0's deprecated <experimental/string_view> [Closed]

> Username: gnaggnoyil  
> Created at: 2018-02-25 14:08:38 UTC  
> Updated at: 2018-11-07 06:48:08 UTC  
> Closed at: 2018-11-07 06:48:08 UTC  
> Url: https://github.com/boostorg/asio/pull/91  

Libc++ 7.0 keeps `<experimental/string_view>` header but deprecates it with an `#error`. This patch is intended to fix this problem. Issue #82 should be fixed after applying this patch.

---

## Comment 1

> Username: knejadfard  
> Created_at: 2018-03-30 14:28:24 UTC  
> Url: https://github.com/boostorg/asio/pull/91#issuecomment-377535859  

Confirming that this change fixes the issue for me. My compiler details:  
  
clang version 7.0.0 (git@github.com:llvm-mirror/clang.git 297171608bc38595e88b07f52e496cf88c818d42) (git@github.com:llvm-mirror/llvm.git 805514b4e0bf71c6a5c6430e422809e2868b2c52)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
  
libcxx revision: 4e177b9  
Boost version: 1.66.0

---

## Comment 2

> Username: rwols  
> Created_at: 2018-04-02 15:27:57 UTC  
> Url: https://github.com/boostorg/asio/pull/91#issuecomment-377955453  

I'm guessing this is fixed with https://github.com/boostorg/asio/commit/b5b17a67f0aa29f5156324d5e8a73dd8669a5a51 now?

---

## Comment 3

> Username: jbeich  
> Created_at: 2018-08-06 14:36:16 UTC  
> Url: https://github.com/boostorg/asio/pull/91#issuecomment-410729985  

No, Asio tries to use `<experimental/string_view>` in C++14 mode which is default for Clang 6 or later.

---

## Review 4 [Commented]

> Username: jbeich  
> Created_at: 2018-08-08 11:26:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/asio/pull/91#pullrequestreview-144371340  

📁 include/boost/asio/detail/config.hpp

```diff
 779 |+ #     if defined(_LIBCPP_LFTS_STRING_VIEW)
 780 |+ #      define BOOST_ASIO_HAS_STD_EXPERIMENTAL_STRING_VIEW 1
 781 |+ #      define BOOST_ASIO_HAS_STD_STRING_VIEW 1
```

> Username: jbeich  
> Created_at: 2018-08-08 11:16:55 UTC  
> Updated_at: 2018-09-20 18:18:03 UTC  
> Url: https://github.com/boostorg/asio/pull/91#discussion_r208543284  

Redundant given separate `__has_include(<string_view>)` conditional later.

---

📁 include/boost/asio/detail/config.hpp

```diff
 777 |- #     define BOOST_ASIO_HAS_STD_STRING_VIEW 1
 778 |- #     define BOOST_ASIO_HAS_STD_EXPERIMENTAL_STRING_VIEW 1
 779 |+ #     if defined(_LIBCPP_LFTS_STRING_VIEW)
```

> Username: jbeich  
> Created_at: 2018-08-08 11:19:19 UTC  
> Updated_at: 2018-09-20 18:18:03 UTC  
> Url: https://github.com/boostorg/asio/pull/91#discussion_r208543848  

`_LIBCPP_LFTS_STRING_VIEW` is the guard defined by `#include <experimental/string_view>` but `#include` cannot be used without bumping into `#error` on libc++ >= 7.


---

## Comment 5

> Username: gnaggnoyil  
> Created_at: 2018-08-17 08:13:53 UTC  
> Updated_at: 2018-08-17 08:16:34 UTC  
> Url: https://github.com/boostorg/asio/pull/91#issuecomment-413792320  

Updated header detection logic. Now `config.hpp` should correctly detect the correct `<string_view>` header to use no matter in which version Clang/libc++ is, and in which C++ standard mode when compiling.

---

## Review 6 [Commented]

> Username: dec1  
> Created_at: 2018-09-20 11:28:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/asio/pull/91#pullrequestreview-157224300  

shouldn't this be `if (__clang_major__ < 7)` ?

---

## Comment 7

> Username: bebuch  
> Created_at: 2018-10-15 11:20:01 UTC  
> Url: https://github.com/boostorg/asio/pull/91#issuecomment-429811697  

Confirming that this patch fixes the issue for me.  
  
Tested with boost develop branch and with LLVM 7, LLVM 6 and GCC 8.2.1.  
  
```  
$ g++ --version  
g++ (GCC) 8.2.1 20181012  
Copyright (C) 2018 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
$ clang++ --version  
clang version 7.0.0 (http://llvm.org/git/clang.git 0513b409d5e34b2d2a28ae21b6d620cc52de0e57) (http://llvm.org/git/llvm.git 65ce2e56889af84e8be8e311f484a4dfe4b62d7a)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
  
$ clang++-6 --version  
clang version 6.0.0 (http://llvm.org/git/clang.git ff0c0d8ab3e316bb6e2741fedb3b545e198eab7a) (http://llvm.org/git/llvm.git 089d4c0c490687db6c75f1d074e99c4d42936a50)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
```  
  
Please merge!  
  
Can you also merge this into the last release and provide an update (1.68.1)? Asio and Beast are hardly usable with clang 7 without that patch.

---

## Review 8 [Approved]

> Username: bebuch  
> Created_at: 2018-10-25 09:47:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/asio/pull/91#pullrequestreview-168295683  

Tested with boost develop branch and with LLVM 7, LLVM 6 and GCC 8.2.1.

---

## Comment 9

> Username: michaelwegner  
> Created_at: 2018-10-27 18:36:57 UTC  
> Url: https://github.com/boostorg/asio/pull/91#issuecomment-433644876  

Merging this would be greatly appreciated!

---

## Comment 10

> Username: bebuch  
> Created_at: 2018-11-06 10:09:09 UTC  
> Url: https://github.com/boostorg/asio/pull/91#issuecomment-436199328  

fixed by 43874d5497414c67655d901e48c939ef01337edb, please close

---
