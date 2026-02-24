# #28 Polynomial: shift operators and odd/even predicates [Merged]

> Username: jeremy-murphy  
> Created at: 2016-03-28 05:10:52 UTC  
> Updated at: 2016-05-09 17:44:00 UTC  
> Merged at: 2016-05-09 17:43:34 UTC  
> Closed at: 2016-05-09 17:43:34 UTC  
> Url: https://github.com/boostorg/math/pull/28  

Shifting bits is a natural enough operation on a binary digital computer. In the binary representation of integers, a right shift removes a factor of 2, or halves. A more abstract way of thinking about this is that it removes the smallest prime factor, which allows for translation of the shift operator into other domains such as polynomials. The smallest prime factor of a polynomial is _x_, so removal equates to decreasing the degree of all terms and thus effectively dropping the constant term. Thus, we can think of polynomials as being odd or even based on the value of the constant term: odd if it is non-zero and even otherwise. Each right shift reduces the degree of the polynomial, eventually reducing to the zero polynomial.  
  
This PR adds left-shift (`<<=` and `<<`) and right-shift (`>>=` and `>>`) operators and `odd()`/`even()` predicates for the `polynomial` class.   
  
As well as being useful for general abstract algebraic and number theoretical fun, they are specifically useful for application of the Stein gcd algorithm.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2016-03-28 05:11:47 UTC  
> Url: https://github.com/boostorg/math/pull/28#issuecomment-202237432  

I'll add documentation/examples once we agree on the interface and implementation.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2016-03-28 12:21:00 UTC  
> Url: https://github.com/boostorg/math/pull/28#issuecomment-202362016  

That all looks reasonable to me.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2016-04-06 18:18:13 UTC  
> Url: https://github.com/boostorg/math/pull/28#issuecomment-206500996  

Pushed to branch gcd.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2016-04-09 15:29:24 UTC  
> Url: https://github.com/boostorg/math/pull/28#issuecomment-207807010  

Apart from some polishing of the example, I think this PR is about done. Any more thoughts on it?

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2016-04-09 17:02:13 UTC  
> Url: https://github.com/boostorg/math/pull/28#issuecomment-207818097  

It's fine, I just want to leave develop stable until after the release, hence merging to the gcd branch for now.

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2016-05-07 08:29:42 UTC  
> Url: https://github.com/boostorg/math/pull/28#discussion_r62413889  

I merged the test for self-multiplication into the general test of multiplication.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2016-05-09 17:44:00 UTC  
> Url: https://github.com/boostorg/math/pull/28#issuecomment-217935487  

All merged to develop now - thanks!

---
