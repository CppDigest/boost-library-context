# #132 Fixes wrong type for mutex in regex v5 [Merged]

> Username: ClaymorePT  
> Created at: 2021-06-03 22:30:11 UTC  
> Updated at: 2021-06-12 21:21:29 UTC  
> Merged at: 2021-06-11 17:12:45 UTC  
> Closed at: 2021-06-11 17:12:45 UTC  
> Url: https://github.com/boostorg/regex/pull/132  

With the Boost.Regex to header-only library, the declaration of a mutex that should have been changed from `boost::static_mutex` to `std::mutex` was left behind. This was preventing regex from being built for older arm platforms [[1]]  
  
[1]: https://github.com/openwrt/packages/issues/15725  
  
Signed-off-by: Carlos Miguel Ferreira <carlosmf.pt@gmail.com>

---

## Comment 1

> Username: gordonb3  
> Created_at: 2021-06-08 14:36:07 UTC  
> Url: https://github.com/boostorg/regex/pull/132#issuecomment-856823802  

Doesn't seem to work as expected as the error is practically the same:  
  
```  
  
    "armv5tel-softfloat-linux-gnueabi-g++"   -fvisibility-inlines-hidden -O2 -pipe -fomit-frame-pointer -march=armv5te -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_REGEX_DYN_LINK=1 -DNDEBUG  -I"."  -c -o "bin.v2/libs/regex/build/gcc-10.3/gentoorelease/pch-off/threading-multi/visibility-hidden/posix_api.o" "libs/regex/build/../src/posix_api.cpp"  
  
In file included from ./boost/regex/v5/perl_matcher_non_recursive.hpp:23,  
                 from ./boost/regex/v5/perl_matcher.hpp:572,  
                 from ./boost/regex/v5/regex.hpp:45,  
                 from ./boost/regex.hpp:34,  
                 from libs/regex/build/../src/posix_api.cpp:22:  
./boost/regex/v5/mem_block_cache.hpp:91:11: error: ‘mutex’ in namespace ‘boost’ does not name a type  
   91 |    boost::mutex mut;  
      |           ^~~~~  
./boost/regex/v5/mem_block_cache.hpp: In member function ‘void* boost::re_detail_500::mem_block_cache::get()’:  
./boost/regex/v5/mem_block_cache.hpp:106:37: error: ‘mut’ was not declared in this scope; did you mean ‘put’?  
  106 |       std::lock_guard<std::mutex> g(mut);  
      |                                     ^~~  
      |                                     put  
./boost/regex/v5/mem_block_cache.hpp: In member function ‘void boost::re_detail_500::mem_block_cache::put(void*)’:  
./boost/regex/v5/mem_block_cache.hpp:120:37: error: ‘mut’ was not declared in this scope; did you mean ‘put’?  
  120 |       std::lock_guard<std::mutex> g(mut);  
      |                                     ^~~  
      |                                     put  
  
```

---

## Comment 2

> Username: gordonb3  
> Created_at: 2021-06-08 14:48:34 UTC  
> Url: https://github.com/boostorg/regex/pull/132#issuecomment-856834138  

Strike that previous remark. I manually edited the file and forgot to change the namespace from `boost` to `std`.  
  
Excellent work! Thank you very much.

---

## Comment 3

> Username: ClaymorePT  
> Created_at: 2021-06-09 13:01:02 UTC  
> Url: https://github.com/boostorg/regex/pull/132#issuecomment-857673864  

@gordonb3 Thanks for the review!

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-06-11 17:19:45 UTC  
> Url: https://github.com/boostorg/regex/pull/132#issuecomment-859728252  

Thanks for this!

---

## Comment 5

> Username: ClaymorePT  
> Created_at: 2021-06-12 18:37:26 UTC  
> Url: https://github.com/boostorg/regex/pull/132#issuecomment-860092456  

@jzmaddock glad I could help!

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2021-06-12 21:21:28 UTC  
> Url: https://github.com/boostorg/regex/pull/132#issuecomment-860111166  

Thanks for the correction - much appreciated!  
  
<http://www.avg.com/email-signature?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
Virus-free.  
www.avg.com  
<http://www.avg.com/email-signature?utm_medium=email&utm_source=link&utm_campaign=sig-email&utm_content=webmail>  
<#DAB4FAD8-2DD7-40BB-A1B8-4E2AA1F9FDF2>  
  
On Sat, 12 Jun 2021 at 19:37, Carlos Miguel Ferreira <  
***@***.***> wrote:  
  
> @jzmaddock <https://github.com/jzmaddock> glad I could help!  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/regex/pull/132#issuecomment-860092456>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABGHSOB6KUJVBXU4J7PV2TDTSOSPHANCNFSM46BUE4IA>  
> .  
>

---
