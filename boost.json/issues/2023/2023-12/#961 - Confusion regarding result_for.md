# #961 - Confusion regarding result_for [Closed]

> Username: panic-sell  
> Created at: 2023-12-19 23:49:09 UTC  
> Updated at: 2023-12-20 15:52:27 UTC  
> Closed at: 2023-12-20 15:52:27 UTC  
> Url: https://github.com/boostorg/json/issues/961  

I'm having trouble understanding the purpose of `result_for`.  
  
Per docs at https://www.boost.org/doc/libs/1_83_0/libs/json/doc/html/json/ref/boost__json__result_for.html: `The purpose of this trait is to let users provide non-throwing conversions for their types without creating a physical dependency on Boost.Json`. How can someone **not** create a physical dependency on Boost.Json if they are already using this library?  
  
If `result_for<T, value>::type` is identical to `result<T>`, the former is unnecessary extra typing. If I'm working on a project that uses Boost.Json facilities to perform JSON conversions (i.e. my project depends on Boost.Json), it seems to me there's no reason to prefer `result_for` over `result`.  
  
Maybe I'm just missing something really obvious.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-12-19 23:56:12 UTC  
> Url: https://github.com/boostorg/json/issues/961#issuecomment-1863631658  

You can forward-declare `result_for`. See for example how it's done in Variant2: https://github.com/boostorg/variant2/blob/9e4234bfd5688c0272b641954ab2f23f8ce9c613/include/boost/variant2/variant.hpp#L2430-L2530

---

## Comment 2

> Username: panic-sell  
> Created at: 2023-12-20 00:28:13 UTC  
> Url: https://github.com/boostorg/json/issues/961#issuecomment-1863656171  

Thanks. IIUC, by forward declaring all Boost.Json names and only implementing `tag_invoke()` in terms of those forward declarations, we can avoid making our custom types depend on Boost.Json  
  
That said, I notice examples in https://www.boost.org/doc/libs/1_83_0/libs/json/doc/html/json/conversion/non_throwing_conversions.html use `result_for` (despite depending on Boost.Json per calls to `jv.get_array()`). Is the recommendation to prefer `result_for` over `result` even when code does depend on Boost.Json? If so, why?

---

## Comment 3

> Username: pdimov  
> Created at: 2023-12-20 00:45:08 UTC  
> Url: https://github.com/boostorg/json/issues/961#issuecomment-1863667988  

The same example does use `result` directly inside the function; `result_for` in the signature is probably only used to match the declaration of `tag_invoke` given above.  
  
I can't think of any reason to prefer `result_for` if you don't mind including JSON (or System) headers.

---

## Comment 4

> Username: grisumbras  
> Created at: 2023-12-20 06:53:27 UTC  
> Url: https://github.com/boostorg/json/issues/961#issuecomment-1863943557  

Yes, there's absolutely no reason to prefer `result_for` over `result` If you're including JSON headers. As @pdimov suggested, the linked example uses `result_for` in the return type to match the signature, in order to be less confusing for the user.

---

## Comment 5

> Username: panic-sell  
> Created at: 2023-12-20 15:52:27 UTC  
> Url: https://github.com/boostorg/json/issues/961#issuecomment-1864724186  

Awesome, thanks for the explanation.
