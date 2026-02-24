# #37 - Fix handling of extreme values [Closed]

> Username: mborland  
> Created at: 2023-05-12 11:04:41 UTC  
> Updated at: 2023-05-17 15:34:59 UTC  
> Closed at: 2023-05-17 15:34:59 UTC  
> Url: https://github.com/boostorg/charconv/issues/37  

The C and C++ standards are clear that for a floating-point type that supports infinities (like IEEE types) all values are in the representable range. So there can never be out of range results (for all implementations supporting IEEE floats, i.e. most of them).  
  
e.g. 1e-9999 should be 0 not `ERANGE`. It is within range but unrepresentable.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-05-12 14:17:43 UTC  
> Url: https://github.com/boostorg/charconv/issues/37#issuecomment-1545820894  

See https://cplusplus.github.io/LWG/issue3081.  
  
At the moment, the committee consensus seems to be that for 1e-9999, `value` should be set to +0.0, but `ec` should be ERANGE (for -1e-9999, -0.0.)  
  
For 1e+9999, there's consensus that `ec` should be ERANGE to indicate overflow, but what `value` needs to be set to is not yet established. I (and others) prefer HUGE_VAL, which is consistent with `strtod` and many other stdlib functions. The issue above proposes `numeric_limits<T>::max()`, which is technically correct according to the spec (this is closer to any finite value than `inf` would be), but hardly useful.  
  
https://isocpp.org/files/papers/P2827R0.html proposes +1.0, which I find ridiculous.  
  
At the moment, I think we need to use +0/-0, ERANGE on underflow, and +-HUGE_VAL, ERANGE on overflow. When issue 3081 is resolved, we'll switch to whatever it says.
