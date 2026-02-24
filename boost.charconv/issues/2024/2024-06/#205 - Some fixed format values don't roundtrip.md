# #205 - Some fixed format values don't roundtrip [Closed]

> Username: mborland  
> Created at: 2024-06-20 18:39:24 UTC  
> Updated at: 2024-06-21 13:30:00 UTC  
> Closed at: 2024-06-21 13:30:00 UTC  
> Url: https://github.com/boostorg/charconv/issues/205  

If the value of `abs(value_struct.exponent)` is greater than `num_dig` we can lose some digits e.g. `2.48386e-20` returns as 2.48386F and `2.12157e+19F` returns 2.12157F.
