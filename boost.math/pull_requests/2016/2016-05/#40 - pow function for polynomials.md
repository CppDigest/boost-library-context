# #40 pow function for polynomials [Merged]

> Username: kundor  
> Created at: 2016-05-11 21:13:17 UTC  
> Updated at: 2016-05-15 16:03:59 UTC  
> Merged at: 2016-05-15 08:50:07 UTC  
> Closed at: 2016-05-15 08:50:07 UTC  
> Url: https://github.com/boostorg/math/pull/40  

I would like to propose a function  
  
``` C++  
template <class T>  
polynomial<T> pow(polynomial<T> base, int exp);  
```  
  
This can be found via argument-dependent lookup, so that polynomials can be used more generically.  
  
This raises a domain exception (according to the default policy) if the exponent is negative. I thought of trying `pow(base[0], exp)` in the case that `base` is a constant (degree-0 polynomial) and `exp` is negative. This works fine when `T` is `double`, or when `T` is `int` and `base[0]` is ±1.  
But it fails if `pow(T, int)` cannot be found, and gives a huge swath of errors if `T` is from Boost.Multiprecision, and perhaps isn't worth the trouble.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2016-05-11 23:54:29 UTC  
> Updated_at: 2016-05-13 13:06:44 UTC  
> Url: https://github.com/boostorg/math/pull/40#discussion_r62946677  

Btw, I don't think we have to go to this length to specify one and zero after all unless it is inside a generic algorithm where the binary operation is parameterized. I think when you know the operation it's fine to just say  
`polynomial<T> zero;  
polynomial<T> one(T(1));`

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2016-05-12 09:17:25 UTC  
> Updated_at: 2016-05-13 13:06:44 UTC  
> Url: https://github.com/boostorg/math/pull/40#discussion_r62988445  

+1, I'd prefer to simplify that, though it doesn't make much difference in a test case I admit.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2016-05-12 09:20:01 UTC  
> Url: https://github.com/boostorg/math/pull/40#issuecomment-218703923  

What was the issue with raising a multiprecision type to an integer power - that should work (via ADL)?  
  
You would still have a problem when T is an integer though, as it would likely call pow(double, int) which would throw away digits when T has more than 53-bit precision.  So the existing code is probably just fine, I was just curious what the multiprecision issue was.

---

## Comment 4

> Username: kundor  
> Created_at: 2016-05-12 18:07:57 UTC  
> Url: https://github.com/boostorg/math/pull/40#issuecomment-218838880  

I only tried using `pow` for the base type when `exp` is negative (otherwise, the normal approach works fine for degree-0 polynomials.) When `T` is an integer, if `base[0]` is ±1, it gives the right result, and otherwise `T(pow(base[0], exp))` should truncate the result to 0. I caught that and raised a `domain_error`.  
  
I dug into the errors more and found that it's specifically for `cpp_rational`. G++ gives many pages of error messages boiling down to "no matching function for call to `pow`".

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2016-05-15 08:50:04 UTC  
> Url: https://github.com/boostorg/math/pull/40#discussion_r63288422  

Apart from the constraint on `exp` this is after all a generic exponentiation algorithm, which would be preferable to see implemented separately from the context of the polynomial class.  
  
Marshall actually implemented [one version](https://github.com/boostorg/algorithm/blob/develop/include/boost/algorithm/algorithm.hpp) (from Knuth) of this algorithm but it explicitly uses modulo to test for oddness, which is of course a performance drag and a reason why I think `odd` deserves to be a trait function for types.

---

## Comment 6

> Username: kundor  
> Created_at: 2016-05-15 14:35:35 UTC  
> Url: https://github.com/boostorg/math/pull/40#discussion_r63292598  

For an int, I would be surprised if testing `exp & 1` vs `n % 2 == 1` were actually different performance-wise  (I'd expect the compiler to emit the same assembly.) But that version uses `y = x * y` and `x = x * x` instead of `*=`, creating lots of extra temporaries .

---
