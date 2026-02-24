# #244 Avoid ODR violations by using fixed types for protocol constants [Merged]

> Username: Lastique  
> Created at: 2025-09-06 22:41:38 UTC  
> Updated at: 2025-10-06 09:24:42 UTC  
> Merged at: 2025-10-06 02:25:41 UTC  
> Closed at: 2025-10-06 02:25:41 UTC  
> Url: https://github.com/boostorg/cobalt/pull/244  

At least on Linux, constants such as `AF_INET`, `SOCK_STREAM` and `IPPROTO_TCP` are implemented with unnamed enums, which makes these constants have different types in different translation units. This results in ODR violations as `protocol_type` struct ends up defined differently in every TU.  
  
Also, `static_protocol` that is being instantiated with these constants also produces different types in each TU. While this by itself is not an ODR violation, it may generate lots of code and data duplication.  
  
Avoid all of the above by using fixed types for family, type and protocol constants in `protocol_type` and `static_protocol`. Use `int` for each of the types, following POSIX socket(2) signature.  
  
Fixes https://github.com/boostorg/cobalt/issues/242.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2025-09-07 09:26:46 UTC  
> Url: https://github.com/boostorg/cobalt/pull/244#issuecomment-3263618026  

In that case I might as well not bother defining the aliases and just use ints all the way?  
  
The unamed enum seems to be used that way anyhow.

---

## Comment 2

> Username: Lastique  
> Created_at: 2025-09-07 10:25:28 UTC  
> Url: https://github.com/boostorg/cobalt/pull/244#issuecomment-3263664648  

> In that case I might as well not bother defining the aliases and just use ints all the way?  
  
You could, but I kept the typedefs anyway as they are used in many places. It also offers an easy point of customization, if on some odd platform `int` is not appropriate for some reason.

---

## Comment 3

> Username: Lastique  
> Created_at: 2025-09-07 10:42:41 UTC  
> Url: https://github.com/boostorg/cobalt/pull/244#issuecomment-3263682507  

The typedefs are also part of public interface, so removing them would be a breaking change.

---
