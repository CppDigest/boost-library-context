# #153 - Port zlib develop branch changes [Closed]

> Username: vinniefalco  
> Created at: 2016-10-24 23:48:24 UTC  
> Updated at: 2022-06-21 23:22:23 UTC  
> Closed at: 2022-06-21 23:22:23 UTC  
> Url: https://github.com/boostorg/beast/issues/153  

There are a few commits that look like they should be applied to Beast's port of zlib:  
https://github.com/madler/zlib/commits/develop

---

## Comment 1

> Username: xsacha  
> Created at: 2017-06-08 15:52:10 UTC  
> Url: https://github.com/boostorg/beast/issues/153#issuecomment-307145992  

Curious, why has Beast ported zlib? It is only used for tests, correct?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-08 16:44:33 UTC  
> Url: https://github.com/boostorg/beast/issues/153#issuecomment-307160807  

Beast provides a public interface to zlib that is header-only C++11! It needs it internally because it supports the WebSocket permessage-deflate extension. There is a full copy of original zlib (in C form) in the tests directory to use for comparison to make sure that Beast's version contains no bugs.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-06-18 20:59:53 UTC  
> Url: https://github.com/boostorg/beast/issues/153#issuecomment-1159561592  

#2444 already updated whatever changed in zlib. Is there anything else we should do here?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-06-21 23:22:23 UTC  
> Url: https://github.com/boostorg/beast/issues/153#issuecomment-1162455644  

No
