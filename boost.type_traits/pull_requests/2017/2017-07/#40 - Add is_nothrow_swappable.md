# #40 Add is_nothrow_swappable [Merged]

> Username: pdimov  
> Created at: 2017-07-15 16:07:12 UTC  
> Updated at: 2017-11-28 18:17:44 UTC  
> Merged at: 2017-07-16 18:02:45 UTC  
> Closed at: 2017-07-16 18:02:45 UTC  
> Url: https://github.com/boostorg/type_traits/pull/40  

Like the C++17 `is_nothrow_swappable` from `<type_traits>`.

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-07-15 21:29:00 UTC  
> Url: https://github.com/boostorg/type_traits/pull/40#issuecomment-315564485  

All green.

---

## Comment 2

> Username: danieljames  
> Created_at: 2017-11-28 12:03:50 UTC  
> Url: https://github.com/boostorg/type_traits/pull/40#issuecomment-347502691  

Thanks for implementing this. Is there a reason why `is_nothrow_swappable` doesn't have a fallback for older compilers?

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-11-28 13:31:00 UTC  
> Url: https://github.com/boostorg/type_traits/pull/40#issuecomment-347523837  

That always returns `false`?  
  
The reason I haven't included a fallback is that `is_nothrow_swappable` is generally only needed in code that is already C++11 and above (to implement conditional `noexcept`, for instance.)  
  
But if you have a case in which it would be useful to have it on C++03 returning `false`, that's not hard to add, I suppose.

---

## Comment 4

> Username: danieljames  
> Created_at: 2017-11-28 17:03:22 UTC  
> Url: https://github.com/boostorg/type_traits/pull/40#issuecomment-347591699  

I have to use a `swap` implementation when the hash function and equality predicate are nothrow swappable in order to meet the 'noexcept' requirements, but it's not exception safe to use `swap` whenn it can throw. So that's part of the implementation, not just in the `noexcept` declaration.  
  
I'll create a pull request, I just wanted to check if there's a reason not to.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-11-28 18:17:44 UTC  
> Url: https://github.com/boostorg/type_traits/pull/40#issuecomment-347614811  

No reason to not add it as far as I'm concerned; this use case makes perfect sense.

---
