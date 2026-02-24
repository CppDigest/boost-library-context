# #1116 Fix X11 naming clash in roots [Closed]

> Username: mborland  
> Created at: 2024-03-27 09:38:06 UTC  
> Updated at: 2024-03-27 11:29:34 UTC  
> Closed at: 2024-03-27 11:29:27 UTC  
> Url: https://github.com/boostorg/math/pull/1116  

Closes: #1115

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2024-03-27 10:55:21 UTC  
> Url: https://github.com/boostorg/math/pull/1116#issuecomment-2022471646  

Thanks Matt, it's a tiny point, but template names are generally CamelCase by convention, so maybe `ComplexType` instead?  Just makes it easier to Grok the code, as `complex_type` would generally be a local typedef.

---

## Comment 2

> Username: mborland  
> Created_at: 2024-03-27 11:27:15 UTC  
> Url: https://github.com/boostorg/math/pull/1116#issuecomment-2022539007  

> Thanks Matt, it's a tiny point, but template names are generally CamelCase by convention, so maybe `ComplexType` instead? Just makes it easier to Grok the code, as `complex_type` would generally be a local typedef.  
  
For some reason I was thinking that ComplexType would still get pasted over since it has the sub-string.

---
