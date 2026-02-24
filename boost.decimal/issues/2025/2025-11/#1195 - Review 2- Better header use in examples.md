# #1195 - Review 2: Better header use in examples [Closed]

> Username: jzmaddock  
> Created at: 2025-11-04 18:44:27 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-14 13:34:00 UTC  
> Url: https://github.com/boostorg/decimal/issues/1195  

I'd try to avoid using the <boost/decimal.hpp> include at all in the  
examples. We then get complaints on "Boost makes your builds slow".  
Specially, please try to avoid this recommendation in the tutorial

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-14 13:34:00 UTC  
> Url: https://github.com/boostorg/decimal/issues/1195#issuecomment-3532842077  

Every example has been updated with annotations of the specific headers used instead of just the convenience header
