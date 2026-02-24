# #159 Add call operator, .prime(), .integrate(), and move constructor to po… [Merged]

> Username: NAThompson  
> Created at: 2018-11-15 00:06:01 UTC  
> Updated at: 2018-12-13 20:07:15 UTC  
> Merged at: 2018-11-25 01:24:06 UTC  
> Closed at: 2018-11-25 01:24:06 UTC  
> Url: https://github.com/boostorg/math/pull/159  

…lynomial.hpp

---

## Comment 1

> Username: NAThompson  
> Created_at: 2018-11-15 05:00:14 UTC  
> Url: https://github.com/boostorg/math/pull/159#issuecomment-438918119  

Just a couple trivial changes allowed templating on `std::complex<float, double, long double>`, but unfortunately something weird is happening for complex multiprecision.  
  
On another note, is there anything that's clearly isn't going to work with complex numbers? Certainly differentiation and integration are the same, and Horner's looks the same, but are there any landmines we could hit?

---
