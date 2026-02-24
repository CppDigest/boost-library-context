# #96 Feature/lambda [Merged]

> Username: joaquintides  
> Created at: 2024-10-03 13:37:06 UTC  
> Updated at: 2024-10-14 15:15:15 UTC  
> Merged at: 2024-10-14 15:15:15 UTC  
> Closed at: 2024-10-14 15:15:15 UTC  
> Url: https://github.com/boostorg/mp11/pull/96  

Closes #93. Points for discussion:  
  
* Currently, `mp_bind` expressions are opaque to `mp_lambda`. For instance, `mp_lambda<mp_bind<std::pair, _1, _2>>::fn<char, int>` evaluates to `mp_bind<std::pair, _1, _2>`. We may decide to see through `mp_bind` and related utilities (my personal inclination is to leave it as is).  
* Independently of the above, we can provide `mp_protect` (`mp_quote` is taken) so that, for instance, `mp_lambda<mp_protect<_1>>::fn<int>` evaluates to `_1`.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-10-14 08:04:19 UTC  
> Url: https://github.com/boostorg/mp11/pull/96#issuecomment-2410367630  

Why are you making `X(_1)` "work" for _1=void? There's no `X(void)` type, that's a C-compatible way to spell `X()`. This shouldn't work.

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-10-14 08:08:13 UTC  
> Url: https://github.com/boostorg/mp11/pull/96#issuecomment-2410375717  

See https://godbolt.org/z/Pzssh44xa

---

## Comment 3

> Username: joaquintides  
> Created_at: 2024-10-14 08:27:21 UTC  
> Updated_at: 2024-10-14 08:27:36 UTC  
> Url: https://github.com/boostorg/mp11/pull/96#issuecomment-2410422222  

Yes, you're right, I added that as a sort of extension via `lambda_devoid_args`. I can easily remove it if so you wish.

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-10-14 08:32:28 UTC  
> Url: https://github.com/boostorg/mp11/pull/96#issuecomment-2410434363  

I so wish, yes.

---
