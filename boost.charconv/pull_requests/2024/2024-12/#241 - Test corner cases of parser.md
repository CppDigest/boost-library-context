# #241 Test corner cases of parser [Open]

> Username: Flamefire  
> Created at: 2024-12-28 12:39:47 UTC  
> Updated at: 2025-01-11 10:52:26 UTC  
> Url: https://github.com/boostorg/charconv/pull/241  

Inspecting the function I see some corner cases that could yield wrong values and/or overflow.  
  
What I suspect:  
  
- There is an off-by-one error in the buffer size which causes early truncation of the significant  
- The exponent might overflow if it is too large. By using a type with at least 20bits this likely does not happen in practice, so I guess that should be enforced.  
- However for large strings the exponent might still overflow or even e.g. `leading_zero_powers` or `extra_zeroes` might already  
  
I came up with some tests that should exercise the corner cases. Please check if they make sense so far before I go into more details

---
