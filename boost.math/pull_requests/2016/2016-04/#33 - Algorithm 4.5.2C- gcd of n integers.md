# #33 Algorithm 4.5.2C: gcd of n integers [Merged]

> Username: jeremy-murphy  
> Created at: 2016-04-14 21:33:13 UTC  
> Updated at: 2016-05-09 17:59:33 UTC  
> Merged at: 2016-05-09 17:59:33 UTC  
> Closed at: 2016-05-09 17:59:33 UTC  
> Url: https://github.com/boostorg/math/pull/33  

A fairly trivial algorithm but necessary for calculating the gcd of polynomials over a unique factorization domain.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2016-04-14 21:35:44 UTC  
> Url: https://github.com/boostorg/math/pull/33#issuecomment-210158294  

What do you think of the name, `gcd_n`? I thought about calling it just `gcd` but it seemed like a lot of template magic to distinguish it from the other 2-arity `gcd` functions, and after all I guess it is a different algorithm.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2016-04-17 10:59:35 UTC  
> Url: https://github.com/boostorg/math/pull/33#issuecomment-211000828  

Name is a good question, in C++11 land we could make gcd a variadic template that accepts n arguments, and I can see genuine uses for this, but your range-based version has good uses to.  
  
Maybe ask on the mailing list for suggestions?

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2016-05-07 01:00:19 UTC  
> Updated_at: 2016-05-07 02:30:40 UTC  
> Url: https://github.com/boostorg/math/pull/33#issuecomment-217594996  

I'm happy with the name `gcd_range`, I'm just still fiddling with the interface.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2016-05-07 04:40:21 UTC  
> Url: https://github.com/boostorg/math/pull/33#issuecomment-217606415  

OK, I've changed it to return the iterator position at termination too.

---
