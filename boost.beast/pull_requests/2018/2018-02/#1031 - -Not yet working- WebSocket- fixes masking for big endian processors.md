# #1031 *Not yet working* WebSocket: fixes masking for big endian processors [Closed]

> Username: smipi1  
> Created at: 2018-02-19 16:21:32 UTC  
> Updated at: 2018-02-21 22:50:18 UTC  
> Closed at: 2018-02-21 22:50:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1031  



---

## Comment 1

> Username: vinniefalco  
> Created_at: 2018-02-19 16:26:55 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366742429  

I think this is the wrong approach. Integers are by their nature endian-independent with respect to mathematical operations. Shifting an integer to the right by a certain number of bits has the same effect on the value no matter the platform. The code was _supposed_ to be written in a way that is endian-agnostic by taking advantage of this fact. I see what you've done with `byte_at` but I think it would be better to perform the correct transformation in `prepare_key` instead, so that the masking code is not changed.

---

## Comment 2

> Username: smipi1  
> Created_at: 2018-02-19 16:34:28 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366744510  

The problem with the current implementation on develop, is that it uses the key as an integer, but it also ends up exposing the key with regard to the underlying memory representation.

---

## Comment 3

> Username: smipi1  
> Created_at: 2018-02-19 16:35:16 UTC  
> Updated_at: 2018-02-19 16:35:39 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366744680  

It might be better not to treat the key as an integer at all, but a buffer. That keeps the key and the buffer you are trying to mask consistent.

---

## Comment 4

> Username: smipi1  
> Created_at: 2018-02-19 16:36:44 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366745052  

We have something that works, but it completely removes the optimizations for 32 and 64 bit keys.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2018-02-19 16:40:59 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366746142  

I see what you mean, this looks troublesome:  
```  
        *reinterpret_cast<std::uint32_t*>(p) ^= key;  
```  
  
I could treat the key as a buffer instead but then we couldn't perform the mask operation 4 or 8 bytes at a time without relying on undefined behavior. This optimization should not be disregarded, it is a hot path for servers.

---

## Comment 6

> Username: smipi1  
> Created_at: 2018-02-19 16:46:24 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366747549  

I understand. That was the approach I was trying to take. Make sure that the compiled code on x86 does not change.

---

## Comment 7

> Username: smipi1  
> Created_at: 2018-02-19 16:47:22 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366747801  

#1032 works for all architectures, and illustrates what the code conceptually is trying to do.

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2018-02-19 16:48:31 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366748088  

My thinking is to change the key to be stored little-endian always, so that this line works:  
```  
  *reinterpret_cast<std::uint32_t*>(p) ^= key;  
```  
And then just change the preamble and postambles to access the key as a buffer instead of using shifts

---

## Comment 9

> Username: smipi1  
> Created_at: 2018-02-19 16:51:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366748749  

The following:  
  
    *reinterpret_cast<std::uint32_t*>(p) ^= key;  
  
Immediately makes the assumption that the key is an endian-less buffer.

---

## Comment 10

> Username: smipi1  
> Created_at: 2018-02-19 16:52:01 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366748995  

If the pre and post ambles cast the key to a buffer and then access the bytes, it should be okay.

---

## Comment 11

> Username: smipi1  
> Created_at: 2018-02-19 16:54:37 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366749665  

The only trick remaining then lies in frame.hpp. It should use a native_uint32_buf_t buffer cast to be consistent with this assumption.

---

## Comment 12

> Username: vinniefalco  
> Created_at: 2018-02-19 16:58:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366750563  

I think the key rotation also has to reverse direction, no?

---

## Comment 13

> Username: vinniefalco  
> Created_at: 2018-02-19 17:00:07 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366751108  

Note that the masking algorithms are also used to encode frames when the stream is operating in client mode, something in `write` (frame.hpp) might need adjusting as well

---

## Comment 14

> Username: smipi1  
> Created_at: 2018-02-19 17:00:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366751280  

Therein lies the tricky problem. Shifting an integer, but treating it like a buffer results in endianness being exposed. So yes... the direction should reverse for both the shift and the barrel rotate operations.  
  
This all is a result of assuming that the key can be directly applied with an XOR.

---

## Comment 15

> Username: smipi1  
> Created_at: 2018-02-19 17:02:08 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366751644  

That is what I was trying to abstract away by extracting the rotate_key and byte_at functions.

---

## Comment 16

> Username: vinniefalco  
> Created_at: 2018-02-19 17:02:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366751691  

Well instead of shifting in the preamble and postamble we could just alias the key to an array of bytes and access the bytes individually. The only ifdef would need to be on the rotate function

---

## Comment 17

> Username: smipi1  
> Created_at: 2018-02-19 17:03:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366751897  

Agreed, unless you rotate in array-of-bytes space

---

## Comment 18

> Username: smipi1  
> Created_at: 2018-02-19 17:03:57 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366752060  

That might be cleanest.

---

## Comment 19

> Username: smipi1  
> Created_at: 2018-02-19 17:07:38 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366753032  

Should I clean it up and send another PR?

---

## Comment 20

> Username: vinniefalco  
> Created_at: 2018-02-19 17:08:27 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366753221  

If it isn't too much trouble, that would be great. So to be clear, we're talking about modifying the original implementation to keep the fast 32 and 64 bit functions but just make them work with both endian types?

---

## Comment 21

> Username: smipi1  
> Created_at: 2018-02-19 17:18:05 UTC  
> Updated_at: 2018-02-19 17:18:21 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366755716  

That's the idea. I will go with the assumption that the key is an integer representation of a native buffer. That makes it consistent with:  
  
    *reinterpret_cast<std::uint32_t*>(p) ^= key;  
  
To be consistent with this, the key will be copied to the header as a buffer using:  
  
    ::new(&b[n]) native_uint32_buf_t{(std::uint32_t)fh.key};  
  
The head and the tail will use an aliased uint8_t array cast of the key to correctly apply the mask and perform the rotation.

---

## Comment 22

> Username: smipi1  
> Created_at: 2018-02-19 17:20:22 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366756405  

Agreed?

---

## Comment 23

> Username: vinniefalco  
> Created_at: 2018-02-19 17:24:20 UTC  
> Updated_at: 2018-02-19 17:24:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366757493  

Yes mostly, although I would rotate the prepared key as an integer using your routines `ror`, `rol`, and `rotate_key` since the key rotation happens inside the loop.

---

## Comment 24

> Username: smipi1  
> Created_at: 2018-02-19 17:24:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366757549  

Also... Should I also remove the redundant endian conversion funcions in frame.hpp?

---

## Comment 25

> Username: vinniefalco  
> Created_at: 2018-02-19 17:25:44 UTC  
> Updated_at: 2018-02-19 17:26:09 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366757872  

>Should I also remove the redundant endian conversion funcions in frame.hpp?  
  
I think so, but note that `fh.key` should have the same value for a given mask no matter the platform. It is the "prepared key" that will be endian-less. That key is generated in `prepare_key`.  
  
The prepared key is what gets passed to `mask_inplace`. The value `fh.key` is never passed to the masking routines.

---

## Comment 26

> Username: smipi1  
> Created_at: 2018-02-19 17:27:57 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366758453  

Off course. `fh.key` is big endian (network native), but the prepared key is whatever is native to the host.

---

## Comment 27

> Username: smipi1  
> Created_at: 2018-02-19 17:29:24 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366758826  

Just to check that we understand each other correctly: The `fh.key` as sent over the wire should have the same byte order as the masked buffer being sent over the wire, right?

---

## Comment 28

> Username: smipi1  
> Created_at: 2018-02-19 17:32:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366759634  

I am also missing where the key rotation is being performed in a loop... Are you referring to the fact that `mask_inplace_fast` might be called in a loop?

---

## Comment 29

> Username: vinniefalco  
> Created_at: 2018-02-19 19:28:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366784054  

Sorry I misspoke, there is no key rotation in the innermost loop

---

## Comment 30

> Username: smipi1  
> Created_at: 2018-02-19 20:42:51 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-366798244  

Okay. If there is no reason to resort to shifting or barrel shifting for optimization in the fast paths, the code becomes endian independent.

---

## Comment 31

> Username: vinniefalco  
> Created_at: 2018-02-20 17:16:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-367049939  

Any progress on this? Should I attempt the fix?

---

## Comment 32

> Username: vinniefalco  
> Created_at: 2018-02-20 17:46:26 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-367059397  

Ah yes, I remember now why I wrote my own endian routines, because Boost.Endian causes warnings on msvc:  
```  
>C:\Users\vinnie\src\boost\boost/endian/buffers.hpp(282): warning C4127: conditional expression is constant (compiling source file C:\Users\vinnie\src\boost\libs\beast\test\beast\websocket\stream.cpp)  
1>C:\Users\vinnie\src\boost\boost/endian/buffers.hpp(282): note: consider using 'if constexpr' statement instead (compiling source file C:\Users\vinnie\src\boost\libs\beast\test\beast\websocket\stream.cpp)  
1>C:\Users\vinnie\src\boost\boost/endian/buffers.hpp(409): note: see reference to function template instantiation 'T boost::endian::detail::load_little_endian<T,4>(const void *) noexcept' being compiled  
```

---

## Review 33 [Commented]

> Username: vinniefalco  
> Created_at: 2018-02-20 17:52:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1031#pullrequestreview-97925000  

📁 include/boost/beast/websocket/detail/frame.hpp

```diff
 274 |         buffer_copy(buffer(b), cb);
 275 |-         cr.code = big_uint16_to_native(&b[0]);
 275 |+         cr.code = (::new(&b[0]) big_uint16_buf_t())->value();
```

> Username: vinniefalco  
> Created_at: 2018-02-20 17:52:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#discussion_r169403812  

This change causes unit test failures


---

## Comment 34

> Username: vinniefalco  
> Created_at: 2018-02-20 19:08:47 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-367084738  

I believe this will resolve this issue, please check it:  
https://github.com/boostorg/beast/pull/1034

---

## Comment 35

> Username: vinniefalco  
> Created_at: 2018-02-21 22:50:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1031#issuecomment-367504497  

The is resolved in the latest version

---
