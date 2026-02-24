# #187 Update iostream operators [Merged]

> Username: Lastique  
> Created at: 2026-01-03 00:48:13 UTC  
> Updated at: 2026-01-04 07:43:08 UTC  
> Merged at: 2026-01-04 02:02:54 UTC  
> Closed at: 2026-01-04 02:02:54 UTC  
> Url: https://github.com/boostorg/uuid/pull/187  

1. Use `from_chars` to implement `operator>>`. This removes code duplication and is faster, after `from_chars` gets a SIMD version.  
2. Use stream character type for `to_chars` in `operator<<`. This avoids character code conversion in the stream and is faster.

---

## Comment 1

> Username: pdimov  
> Created_at: 2026-01-03 00:51:48 UTC  
> Url: https://github.com/boostorg/uuid/pull/187#issuecomment-3706550025  

This doesn't do the same thing though, which is why I haven't changed it. The stream locale may do something different on `ctype.widen`.

---

## Comment 2

> Username: Lastique  
> Created_at: 2026-01-03 00:59:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/187#issuecomment-3706555023  

Well, my understanding was that `widen` was supposed to produce the (universal, all encompassing) `wchar_t` encoding from (some user or application-specific, narrow) `char`. And vise versa. When we can produce every character type natively, the locale should be redundant, no?

---

## Comment 3

> Username: pdimov  
> Created_at: 2026-01-03 01:07:01 UTC  
> Url: https://github.com/boostorg/uuid/pull/187#issuecomment-3706560736  

The locale can do anything it likes; we don't know the stream encoding. There's no way you could tell that it's "redundant".  
  
I doubt that anyone relies on this in practice, but it's still a breaking change if they do. Which is why I haven't done it.

---

## Comment 4

> Username: Lastique  
> Created_at: 2026-01-03 01:09:14 UTC  
> Url: https://github.com/boostorg/uuid/pull/187#issuecomment-3706562423  

Ok, closing then.

---

## Comment 5

> Username: pdimov  
> Created_at: 2026-01-03 01:30:15 UTC  
> Url: https://github.com/boostorg/uuid/pull/187#issuecomment-3706577448  

Please undelete.

---

## Comment 6

> Username: pdimov  
> Created_at: 2026-01-03 10:24:08 UTC  
> Url: https://github.com/boostorg/uuid/pull/187#issuecomment-3706956473  

Since we know that `widen` is identity for `char`, there's no concern. For the unicode character types, there's also no backward compatibility concern because they aren't "officially" supported as the documentation only allows `char` and `wchar_t`.  
  
This leaves `wchar_t`. We could in principle special case it to work as it did before, but that probably won't be necessary. `wchar_t` is mostly used on Windows, and there its encoding is fixed to UTF-16.  
  
I think we can go ahead with this as-is, and see if anyone complains about the `wchar_t` change. I doubt it.

---
