# #1189 - Review 2: API design. [Closed]

> Username: jzmaddock  
> Created at: 2025-11-04 17:58:08 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-07 15:16:06 UTC  
> Url: https://github.com/boostorg/decimal/issues/1189  

>Considering e.g. decimal32_t, I'm unsure about the rationale behind having both of these constructors:  
  
```  
  template <typename UnsignedInteger, typename Integer>  
  constexpr decimal32_t(UnsignedInteger coefficient, Integer exponent,  
    bool sign = false) noexcept;  
  
  template <typename SignedInteger, typename Integer>  
  constexpr decimal32_t(SignedInteger coefficient, Integer exponent)  
    noexcept;  
```  
>The second seems sufficient. What's the intended use case for the first?  
  
I know you answered this in the review, but I'm still confused, do you need to construct from values larger than 1 << 127 ?  This feels like a useful implementation back door, rather than a good user interface.  At the very least the suggestion to use a descriptive enum for the sign parameter seems wise.

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-05 09:43:04 UTC  
> Url: https://github.com/boostorg/decimal/issues/1189#issuecomment-3490188526  

In the 128-bit case we have a full-fledged software u128 that is used as the bits of the 128-bit types and used extensively in all the operations. We can't rely on the existence of builtin 128-bit types which we need for say all the numeric constants. I'd be willing to bet in the 32 or 64 bit cases we have constants that exceed the signed range. The signed constructor delegates to the unsigned constructor.   
  
```  
template <typename T1, typename T2, std::enable_if_t<!detail::is_unsigned_v<T1> && detail::is_integral_v<T2>, bool>>  
constexpr decimal32_t::decimal32_t(const T1 coeff, const T2 exp) noexcept : decimal32_t(detail::make_positive_unsigned(coeff), exp, coeff < 0) {}  
```  
  
I have also already renamed the sign parameter based on feedback that I think came from reddit, sign is now is_negative. The suggestion to make it an enum class is fine and is easier to make sense of than a bool.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2025-11-05 20:09:32 UTC  
> Url: https://github.com/boostorg/decimal/issues/1189#issuecomment-3493133385  

Thanks Matt.
