# #199 Use underlying type for enums in is_signed/is_unsigned. [Open]

> Username: Romain-Geissler-1A  
> Created at: 2025-09-05 23:38:03 UTC  
> Updated at: 2025-11-24 15:25:41 UTC  
> Url: https://github.com/boostorg/type_traits/pull/199  

With clang >= 21, using is_signed/is_unsigned with "small" enums (having an underlying type smaller than an int) results in strong error, after being reported as warnings for years (see #171).  
  
Since boost::is_signed/is_unsigned differs explicitly from the std one, and can return "true" for enums (contrary to the std one), keep this boost behavior and implement a special check for enums.  
  
This allows in particular boost::lexical_cast to keep working when converting non-scoped enums from int to strings with clang >= 21, see boostorg/lexical_cast#87).

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created_at: 2025-09-14 00:16:56 UTC  
> Url: https://github.com/boostorg/type_traits/pull/199#issuecomment-3288994171  

Ping

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2025-09-26 16:09:04 UTC  
> Url: https://github.com/boostorg/type_traits/pull/199#issuecomment-3339392350  

Ping

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2025-09-27 17:27:40 UTC  
> Url: https://github.com/boostorg/type_traits/pull/199#issuecomment-3341933558  

The problem you have here, is that `underlying_type` gets instantiated unconditionally regardless of the argument, or whether the result is used.

---

## Comment 4

> Username: Romain-Geissler-1A  
> Created_at: 2025-10-31 15:10:37 UTC  
> Url: https://github.com/boostorg/type_traits/pull/199#issuecomment-3473516511  

@jzmaddock I changed a bit the definition, to now use underlying_type only for enums.

---

## Comment 5

> Username: lolipodass  
> Created_at: 2025-11-12 10:32:36 UTC  
> Url: https://github.com/boostorg/type_traits/pull/199#issuecomment-3521228642  

Ping

---

## Comment 6

> Username: Romain-Geissler-1A  
> Created_at: 2025-11-24 15:25:41 UTC  
> Url: https://github.com/boostorg/type_traits/pull/199#issuecomment-3571367933  

Ping

---
