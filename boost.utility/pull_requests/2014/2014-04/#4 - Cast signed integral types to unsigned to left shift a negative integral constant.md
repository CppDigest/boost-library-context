# #4 Cast signed integral types to unsigned to left shift a negative integral constant [Closed]

> Username: BenPope  
> Created at: 2014-04-03 16:09:04 UTC  
> Updated at: 2014-06-16 16:54:50 UTC  
> Closed at: 2014-05-10 14:31:38 UTC  
> Url: https://github.com/boostorg/utility/pull/4  

Clang refuses to compile a left shift of a negative integral constant, so I'll leverage the hopefully sane behaviour of casting to an unsigned type for the intermediate result.  
  
I've intentionally left gcc 4.0.2 out of this as I'll assume it does the right thing and may or may not handle the make_unsigned.  
  
I've tested on linux with Clang 3.4 and 3.5, as well as gcc 4.8.2 all with std=c++11.  
  
Opinions on whether a static_cast would be preferable to a c-style cast are accepted, but since the c-style cast is used throughout, I stuck with that.

---

## Comment 1

> Username: Lastique  
> Created_at: 2014-05-10 14:31:38 UTC  
> Url: https://github.com/boostorg/utility/pull/4#issuecomment-42743313  

Fixed differently in develop: https://github.com/boostorg/utility/commit/991539725e5943b7f280f7ecd7a00aa7f3dc0582.

---
