# #1660 - Wiki instructions on how to build the tests on Macosx do not work [Closed]

> Username: dmorilha-twilio  
> Created at: 2019-07-17 21:06:53 UTC  
> Updated at: 2019-09-09 19:27:02 UTC  
> Closed at: 2019-08-25 20:18:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1660  

Wiki instructions on how to build the tests on Macosx do not work:  
  
```  
$ echo $OPENSSL_ROOT  
/usr/local/opt/openssl  
$ time b2 -j2 libs/beast/test cxxstd=11  
...  
./boost/asio/ssl/detail/openssl_types.hpp:23:10: fatal error: 'openssl/conf.h' file not found  
#include <openssl/conf.h>  
         ^~~~~~~~~~~~~~~~  
1 error generated.  
  
    "clang++" -x c++ -std=c++11 -fvisibility-inlines-hidden -fPIC -m64 -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_DISABLE_BOOST_ARRAY=1 -DBOOST_ASIO_DISABLE_BOOST_BIND=1 -DBOOST_ASIO_DISABLE_BOOST_DATE_TIME=1 -DBOOST_ASIO_DISABLE_BOOST_REGEX=1 -DBOOST_ASIO_NO_DEPRECATED=1 -DBOOST_ASIO_SEPARATE_COMPILATION -DBOOST_BEAST_ALLOW_DEPRECATED -DBOOST_BEAST_SEPARATE_COMPILATION -DBOOST_BEAST_TESTS -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_COROUTINES_DYN_LINK=1 -DBOOST_COROUTINES_NO_DEPRECATION_WARNING=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -I"." -I"libs/beast" -I"libs/beast/test/extern" -c -o "bin.v2/libs/beast/test/beast/ssl.test/clang-darwin-10.0/debug/cxxstd-11-iso/threadapi-pthread/threading-multi/visibility-hidden/ssl.o" "libs/beast/test/beast/ssl.cpp"  
...  
$ find /usr/local/opt/openssl -follow -name conf.h  
/usr/local/opt/openssl/include/openssl/conf.h  
```

---

## Comment 1

> Username: djarek  
> Created at: 2019-07-17 21:16:02 UTC  
> Updated at: 2019-07-17 21:16:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1660#issuecomment-512571873  

Try the instructions from the readme: https://github.com/boostorg/beast#mac-os

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-07-17 21:18:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1660#issuecomment-512572635  

I don't see any wiki instructions. Do you have a link?

---

## Comment 3

> Username: dmorilha-twilio  
> Created at: 2019-07-17 21:25:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1660#issuecomment-512574779  

@vinniefalco https://github.com/boostorg/beast#mac-os

---

## Comment 4

> Username: dmorilha-twilio  
> Created at: 2019-07-17 23:59:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1660#issuecomment-512612242  

I "solved" by linking `/usr/local/opt/openssl/include/openssl` into `/usr/local/include`

---

## Comment 5

> Username: dmorilha-twilio  
> Created at: 2019-07-18 00:33:39 UTC  
> Updated at: 2019-07-18 00:49:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1660#issuecomment-512618697  

to be fair, this sounds more like a problem on **ASIO** than **BEAST** itself.

---

## Comment 6

> Username: djarek  
> Created at: 2019-07-18 17:17:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1660#issuecomment-512908023  

Hmm, this should be working regardless of where you put OpenSSL, as long as you set the root variable correctly. Maybe some weird caching issues with b2?

---

## Comment 7

> Username: dmorilha-twilio  
> Created at: 2019-07-19 18:53:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1660#issuecomment-513338551  

Yeah, eventually complexity piles up and boost, which is suppose to help, starts creating more problems. I am not sure why **b2** even exists given its weak adoption and the presence of solid alternatives. As I solved my side of things, feel free to close this one if you don't think it belongs in here.

---

## Comment 8

> Username: stale[bot]  
> Created at: 2019-08-18 19:29:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1660#issuecomment-522348523  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: stale[bot]  
> Created at: 2019-08-25 20:18:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1660#issuecomment-524660525  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 10

> Username: snoe925  
> Created at: 2019-09-09 19:05:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1660#issuecomment-529621899  

The OS X instructions need to add  
```  
cp ./libs/beast/tools/user-config.jam $HOME  
```  
in order for OPENSSL_ROOT to be read from the environment.  Then the brew installed openssl can be found.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-09-09 19:27:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1660#issuecomment-529629420  

Feel free to edit the wiki? Let me know if it is not editable (it should be writable)
