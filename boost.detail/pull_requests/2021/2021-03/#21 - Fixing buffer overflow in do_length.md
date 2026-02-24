# #21 Fixing buffer overflow in do_length [Closed]

> Username: kevcadieux  
> Created at: 2021-03-14 20:05:37 UTC  
> Updated at: 2021-03-17 11:19:00 UTC  
> Closed at: 2021-03-17 11:15:30 UTC  
> Url: https://github.com/boostorg/detail/pull/21  

This change fixes a buffer overflow in the UTF-8 codecvt facet's `do_length` function that was detected by MSVC's AddressSanitizer when running tests internally. Prior to this change, the character pointed to by `from_end`, which may point to invalid memory, was being dereferenced. The fix is to modify the loop to only dereference a character when we know we haven't yet reached `from_end`.

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-03-15 17:27:33 UTC  
> Updated_at: 2021-03-15 17:29:09 UTC  
> Url: https://github.com/boostorg/detail/pull/21#issuecomment-799604086  

I think it introduces a potentially long loop of decrementing `max_limit` down to zero if there is an incomplete character at the end of `from`. You should break out of the loop when you reach `from_end`.

---

## Comment 2

> Username: Lastique  
> Created_at: 2021-03-15 17:30:34 UTC  
> Url: https://github.com/boostorg/detail/pull/21#issuecomment-799606292  

Nevermind, the code is confusingly written, and I missed the `(from += next_octet_count)` part. Which means the code needs to be simplified.

---

## Comment 3

> Username: kevcadieux  
> Created_at: 2021-03-16 22:13:51 UTC  
> Url: https://github.com/boostorg/detail/pull/21#issuecomment-800648395  

Hello @Lastique, thanks for the feedback. I moved the modification of `from` on its own line.

---

## Comment 4

> Username: Lastique  
> Created_at: 2021-03-17 11:16:05 UTC  
> Updated_at: 2021-03-17 11:19:00 UTC  
> Url: https://github.com/boostorg/detail/pull/21#issuecomment-801000567  

Thanks, I did the change differently to also avoid incrementing the pointer past `from_end`, which is potentially UB. Should be fixed in develop now.

---
