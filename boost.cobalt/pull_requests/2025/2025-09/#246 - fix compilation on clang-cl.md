# #246 [concepts] fix compilation on clang-cl [Closed]

> Username: crueter  
> Created at: 2025-09-08 03:50:06 UTC  
> Updated at: 2025-09-10 17:23:42 UTC  
> Closed at: 2025-09-10 17:23:42 UTC  
> Url: https://github.com/boostorg/cobalt/pull/246  

clang-cl seems to require this to compile.

---

## Comment 1

> Username: mscottmueller  
> Created_at: 2025-09-09 20:06:34 UTC  
> Url: https://github.com/boostorg/cobalt/pull/246#issuecomment-3272107297  

Are you building with BOOST_COBALT_USE_IO_CONTEXT defined?

---

## Comment 2

> Username: crueter  
> Created_at: 2025-09-09 20:18:35 UTC  
> Url: https://github.com/boostorg/cobalt/pull/246#issuecomment-3272139625  

> Are you building with BOOST_COBALT_USE_IO_CONTEXT defined?  
  
I'm building exactly as the default with CMake.

---

## Comment 3

> Username: mscottmueller  
> Created_at: 2025-09-10 02:25:00 UTC  
> Url: https://github.com/boostorg/cobalt/pull/246#issuecomment-3272966988  

Thanks for letting me know. I'm definitely not seeing the same problem you are.

---

## Comment 4

> Username: crueter  
> Created_at: 2025-09-10 02:26:29 UTC  
> Url: https://github.com/boostorg/cobalt/pull/246#issuecomment-3272969113  

> Thanks for letting me know. I'm definitely not seeing the same problem you are.  
  
The project I'm building this from (as a submodule) has more restrictive error settings, so I'm sure that's it.

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2025-09-10 17:20:58 UTC  
> Url: https://github.com/boostorg/cobalt/pull/246#issuecomment-3275830238  

That fixes the wrong thing. Asio deduces concepts support incorreclty and thus does not declare the concept. This can be fixed with an asio config flag.

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2025-09-10 17:23:35 UTC  
> Url: https://github.com/boostorg/cobalt/pull/246#issuecomment-3275839323  

See #225.

---
