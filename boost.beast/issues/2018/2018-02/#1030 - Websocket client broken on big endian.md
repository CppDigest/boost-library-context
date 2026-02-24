# #1030 - Websocket client broken on big endian [Closed]

> Username: smipi1  
> Created at: 2018-02-19 16:16:17 UTC  
> Updated at: 2018-02-21 16:33:53 UTC  
> Closed at: 2018-02-21 16:33:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1030  

https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/detail/mask.hpp does not have an endian independent implementation. It has been highly optimised for x86 32 and 64 bit, but there is no working implementation that works on all memory models.  
  
### Version of Beast  
  
144  
  
### Steps necessary to reproduce the problem  
  
1. Compile and start the websocket sync or async server example on an x86 (little endian),  
1. Compile and start the websocket sync or async client example on a big-endian processor (E.g. MIPS or PowerPC),  
1. Observe that all data sent by the client to the server arrives corrupted.  
  
### All relevant compiler information  
  
1. On the x86, the server code is compiled with: `c++ (Ubuntu 7.2.0-8ubuntu3) 7.2.0`  
1. On the MIPS (QCA4531), the client code is compiled with: `mips-openwrt-linux-uclibc-c++ (OpenWrt/Linaro GCC 4.8-2014.04 r47070) 4.8.3`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-19 16:19:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1030#issuecomment-366740239  

Thanks for this! I am really surprised, the code was supposed to be endian-independent (i.e. not depend on the layout of integers) but apparently I was mistaken. If you have a proposed fix I would love to see it, otherwise I will get to work on it.

---

## Comment 2

> Username: smipi1  
> Created at: 2018-02-19 16:20:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1030#issuecomment-366740489  

We have a concept implementation, but something is still not quite right. Feel free to help spot the error.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-19 16:22:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1030#issuecomment-366741162  

Well first we should check that the mask is correctly extracted from the frame header and stored in a manner consistent with the endianness expected by the masking routines, see:  
https://github.com/boostorg/beast/blob/1a88cb147bf15541df632ca60f277a02068bae5b/include/boost/beast/websocket/detail/frame.hpp#L218

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-02-19 16:23:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1030#issuecomment-366741504  

and  
https://github.com/boostorg/beast/blob/1a88cb147bf15541df632ca60f277a02068bae5b/include/boost/beast/websocket/impl/stream.ipp#L493

---

## Comment 5

> Username: smipi1  
> Created at: 2018-02-19 16:29:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1030#issuecomment-366743278  

We noticed an inconsistency between frame.hpp and mask.hpp.  
frame.hpp always swaps the endianness of the key, and  
mask applies it as if it is a little endian architecture.  
  
This should be easier to fix if you avoid shifting operations in favour of a buffer cast of the key, but I have not had the time to compare this with the generated asm on x86.

---

## Comment 6

> Username: smipi1  
> Created at: 2018-02-19 16:31:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1030#issuecomment-366743729  

Needless to say... in #1031, we tried to stick to the current implementation, but make the intent clear, but we are missing something in the tail part.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-02-20 19:08:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1030#issuecomment-367084700  

I believe this will resolve this issue, please check it:  
https://github.com/boostorg/beast/pull/1034
