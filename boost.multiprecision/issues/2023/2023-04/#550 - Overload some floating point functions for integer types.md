# #550 - Overload some floating point functions for integer types [Open]

> Username: jzmaddock  
> Created at: 2023-04-21 18:00:07 UTC  
> Updated at: 2023-04-21 18:00:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/550  

Functions such as `isinf` which do not rely on integer->floating point type promotion could be overloaded for non-floating point number types.
