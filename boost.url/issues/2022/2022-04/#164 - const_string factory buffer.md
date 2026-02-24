# #164 - const_string factory buffer [Closed]

> Username: alandefreitas  
> Created at: 2022-04-04 16:30:42 UTC  
> Updated at: 2022-07-26 20:47:13 UTC  
> Closed at: 2022-07-26 20:47:13 UTC  
> Url: https://github.com/boostorg/url/issues/164  

We should consider a small buffer for `const_string` to store a `factory`. At least large enough for a factory using `static_pool::allocator`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-04-04 16:31:43 UTC  
> Url: https://github.com/boostorg/url/issues/164#issuecomment-1087769553  

This solves the problem of always allocating a new shared factory every time the user calls a `url_view` function that returns a decoded string.
