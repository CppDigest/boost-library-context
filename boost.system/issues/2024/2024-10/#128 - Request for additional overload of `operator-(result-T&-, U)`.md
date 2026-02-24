# #128 - Request for additional overload of `operator|(result<T&>, U)` [Closed]

> Username: grisumbras  
> Created at: 2024-10-09 15:27:18 UTC  
> Updated at: 2025-12-30 18:47:03 UTC  
> Closed at: 2025-12-30 18:47:03 UTC  
> Url: https://github.com/boostorg/system/issues/128  

Consider this: https://godbolt.org/z/qK4GTrdYM  
  
The `decay` function is only needed there, because `try_at` returns `result<value const&>`, and the righthand operand of `operator|` is not convertible to `value const&`.  
  
Given that `operator|` is used for providing a default, and the default usually doesn't exist before it is required, most of the time you don't want to return a `result<T&>`, even if the source is a `result<T&>`.  
  
I can suggest adding `result<T> operator|(result<T&>, U)` overload that matches if `U` is convertible to `std::decay_t<T&>`. Same for function overloads.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-12-01 10:10:51 UTC  
> Url: https://github.com/boostorg/system/issues/128#issuecomment-3595696973  

Works now on develop: https://godbolt.org/z/eE6GecoP5

---

## Comment 2

> Username: grisumbras  
> Created at: 2025-12-01 12:02:03 UTC  
> Url: https://github.com/boostorg/system/issues/128#issuecomment-3596157216  

Beautiful
