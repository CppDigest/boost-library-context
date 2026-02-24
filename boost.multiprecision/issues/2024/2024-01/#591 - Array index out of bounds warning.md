# #591 - Array index out of bounds warning [Closed]

> Username: NAThompson  
> Created at: 2024-01-24 23:15:36 UTC  
> Updated at: 2025-06-29 10:17:30 UTC  
> Closed at: 2025-06-29 10:17:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/591  

Found in [this](https://drone.cpp.al/boostorg/math/1633/425/2) build log, in particularly:  
  
```  
../../../boost/multiprecision/cpp_int/bitwise.hpp:598:14: warning: array subscript [7, 1152921504606846975] is outside array bounds of 'boost::multiprecision::backends::cpp_bin_float<50>::rep_type [1]' {aka 'boost::multiprecision::backends::cpp_int_backend<168, 168, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void> [1]'} [-Warray-bounds=]  
  
  598 |    pr[i] = pr[i + offset] >> shift;  
  
      |            ~~^  
```

---

## Comment 1

> Username: ckormanyos  
> Created at: 2025-06-28 19:29:31 UTC  
> Updated at: 2025-06-28 19:30:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/591#issuecomment-3015995840  

Hi @NAThompson   
  
Hi @jzmaddock If I gather correclty, this is redundant with #686, which is cycling now in CI in #702.  
  
I'm going through a bunch of issues now and trying to prioritize them.  
  
Can this be closed?  
  
Cc: @mborland

---

## Comment 2

> Username: jzmaddock  
> Created at: 2025-06-29 10:17:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/591#issuecomment-3016548853  

Yep, out of date.
