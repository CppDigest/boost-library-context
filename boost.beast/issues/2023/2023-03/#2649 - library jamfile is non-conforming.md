# #2649 - library jamfile is non-conforming [Closed]

> Username: vinniefalco  
> Created at: 2023-03-04 23:42:49 UTC  
> Updated at: 2024-06-06 05:33:32 UTC  
> Closed at: 2023-06-28 04:27:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2649  

The Jamfile for the compiled lib should be in build/Jamfile not the root Jamfile, which probably should be stripped down and/or removed. The library name is "lib-beast" which is also not normal it should be "boost_beast".

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-03-04 23:59:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2649#issuecomment-1454928082  

Also the file test/lib_beast.cpp should be in src/lib_beast.cpp

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-05-17 04:18:24 UTC  
> Updated at: 2023-05-17 04:19:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2649#issuecomment-1550680028  

I think that's a bad idea without having openssl.jam work properly. And I can't get this work.  
  
The reason is that we need a boost wide ssl target, so that dependent libraries, like requests can pick this up.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-05-17 18:20:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2649#issuecomment-1551859377  

Ah yes I knew I had a reason. And I tried to get the openssl to work, but I failed. You will probably succeed though.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-06-28 04:27:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2649#issuecomment-1610693788  

I made openssl.jam work. But I will still keep lib_beast and lib_asio where they are (in root). Not exported and used by example & test. Exporting a beast target is a bad idea until we have something similar for asio.
