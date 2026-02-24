# #241 Install boost_cobalt_io_ssl by default [Merged]

> Username: Lastique  
> Created at: 2025-09-03 00:26:49 UTC  
> Updated at: 2025-12-23 16:13:20 UTC  
> Merged at: 2025-09-07 12:25:45 UTC  
> Closed at: 2025-09-07 12:25:46 UTC  
> Url: https://github.com/boostorg/cobalt/pull/241  

Make sure boost_cobalt_io_ssl library is built and installed by default when Boost as a whole or Boost.Cobalt is built (i.e. when the user runs b2 in libs/cobalt).

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2025-09-07 09:32:31 UTC  
> Url: https://github.com/boostorg/cobalt/pull/241#issuecomment-3263620777  

Does that break the boost build if openssl is not found?

---

## Comment 2

> Username: Lastique  
> Created_at: 2025-09-07 10:23:16 UTC  
> Url: https://github.com/boostorg/cobalt/pull/241#issuecomment-3263660961  

> Does that break the boost build if openssl is not found?  
  
I didn't test that since OpenSSL is pretty much always installed as a dependency of something essential. But you do have `ac.check-library` rules that are supposed to take care of that, don't you?

---

## Comment 3

> Username: Lastique  
> Created_at: 2025-09-07 10:55:28 UTC  
> Updated_at: 2025-09-07 10:58:40 UTC  
> Url: https://github.com/boostorg/cobalt/pull/241#issuecomment-3263688879  

I just tested building Boost without libssl-dev installed and it compiled without errors. During configuration, `b2` said `- ssl                      : no` and `libboost_cobalt_io_ssl.so` was excluded from build.

---
