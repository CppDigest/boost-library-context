# #172 Update design_guide.rst [Merged]

> Username: sdebionne  
> Created at: 2018-12-06 09:49:33 UTC  
> Updated at: 2018-12-16 23:03:36 UTC  
> Merged at: 2018-12-06 12:41:13 UTC  
> Closed at: 2018-12-06 12:41:13 UTC  
> Url: https://github.com/boostorg/gil/pull/172  

Quick fix of the documentation that is not in sync with the code base. Standard type aliases for some integral channels were removed (unknown reason).

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-12-06 10:00:40 UTC  
> Url: https://github.com/boostorg/gil/pull/172#issuecomment-444816290  

Thanks for the update!  
  
> Standard type aliases for some integral channels were removed (unknown reason).  
  
See #83

---

## Review 2 [Commented]

> Username: mloskot  
> Created_at: 2018-12-06 10:02:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/172#pullrequestreview-182160708  

📁 doc/design_guide.rst

```diff
 253 |-   typedef boost::int16_t  bits16s;
 254 |-   typedef boost::int32_t  bits32s;
 246 |+ All built-in integral and floating point types are valid channels.
```

> Username: mloskot  
> Created_at: 2018-12-06 10:02:08 UTC  
> Updated_at: 2018-12-06 11:33:38 UTC  
> Url: https://github.com/boostorg/gil/pull/172#discussion_r239391643  

I'd reword it this way  
  
"All C++11 fundamental integer and float point types are valid channels."

> Username: sdebionne  
> Created_at: 2018-12-06 11:34:25 UTC  
> Updated_at: 2018-12-06 11:34:26 UTC  
> Url: https://github.com/boostorg/gil/pull/172#discussion_r239420206  

Fixed.

> Username: mloskot  
> Created_at: 2018-12-06 12:37:30 UTC  
> Url: https://github.com/boostorg/gil/pull/172#discussion_r239436911  

Thanks


---

## Comment 3

> Username: sdebionne  
> Created_at: 2018-12-06 11:37:56 UTC  
> Url: https://github.com/boostorg/gil/pull/172#issuecomment-444843075  

What I did not understand is the need to introduce this breaking change, the aliases being pretty harmless,  but no big deal...

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-12-06 12:37:21 UTC  
> Updated_at: 2018-12-06 12:40:01 UTC  
> Url: https://github.com/boostorg/gil/pull/172#issuecomment-444857197  

> What I did not understand is the need to introduce this breaking change  
  
Yes, it could be avoided.  
However, we take the (still ongoing, breaking) switch to C++11 as opportunity to apply extensive clean-up overhaul, incl. removal of unnecessary in-house stuff.  
  
Another aspect, maintaining custom for well-known C++ features (i.e. `bits` prefix and `s` suffix) is unnecessary and makes interface less discoverable and self-descriptive.

---

## Review 5 [Approved]

> Username: mloskot  
> Created_at: 2018-12-06 12:37:48 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/172#pullrequestreview-182218261  

LGTM

---
