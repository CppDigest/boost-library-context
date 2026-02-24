# #418 Update multiprecision erf to improve precision and performance. [Merged]

> Username: jzmaddock  
> Created at: 2020-08-13 17:13:28 UTC  
> Updated at: 2020-08-20 17:32:23 UTC  
> Merged at: 2020-08-20 17:21:44 UTC  
> Closed at: 2020-08-20 17:21:44 UTC  
> Url: https://github.com/boostorg/math/pull/418  

See https://github.com/boostorg/multiprecision/issues/264.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-08-13 17:32:14 UTC  
> Updated_at: 2020-08-13 18:02:48 UTC  
> Url: https://github.com/boostorg/math/pull/418#issuecomment-673609763  

@jzmaddock : What about equation (9) and (10) of [this](https://mathworld.wolfram.com/Erf.html)? I have some concern that since the series you're using alternates, the summation condition number can still grow even if Kahan summation ensures it doesn't grow with the number of terms. . .  
  
Also, since you've already wrote the code for the ulp plot, want to drop it into `reporting/accuracy`?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-08-13 18:13:33 UTC  
> Url: https://github.com/boostorg/math/pull/418#issuecomment-673632579  

> What about equation (9) and (10) of this?  
  
I confess I hadn't spotted those - but they're only valid for x << 1, where as the alternating Maclaurin series is more generally valid, and strictly converging even for x slightly above 1: this is actually what we need here as the cutoff for use needed to be x < 1.3 to improve the error rate across the board.  BTW the use of Kahan summation only reduces the error level from ~ 5eps to 2eps.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2020-08-13 18:14:35 UTC  
> Url: https://github.com/boostorg/math/pull/418#issuecomment-673633081  

And yes, I will commit the error plots, not sure whether they belong in multiprecision or here though...

---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-08-13 18:16:11 UTC  
> Updated_at: 2020-08-13 18:18:17 UTC  
> Url: https://github.com/boostorg/math/pull/418#issuecomment-673633834  

> not sure whether they belong in multiprecision or here though...  
  
I'd put them in here; for example a simple change will make the ulps plot work for double precision at that clearly belongs in math.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2020-08-13 18:24:02 UTC  
> Url: https://github.com/boostorg/math/pull/418#issuecomment-673637488  

I *think* this'll work:  
  
```cpp  
template <class T>  
T exp_erf_near_zero(const T&x)  
{  
   using std::exp;  
   Real scale =  constants::one_div_root_pi<T>()*2*x*exp(-x*x);  
   Real sum = 1;  
   Real term = 4*x*x;  
   int64_t n = 1;  
   while (term > std::numeric_limits<Real>::epsilon()) {  
      n += 2;  
      term *= (4*x*x)/n;  
      sum += term;  
   }  
   return sum*scale;  
}  
```

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2020-08-14 09:18:05 UTC  
> Url: https://github.com/boostorg/math/pull/418#issuecomment-673979431  

>I think this'll work:  
  
A quick test shows it's only accurate for x < 1e-8 at double precision, in that domain the Maclaurin series reduces to a single term anyway!  
  
The worst case situation for the Maclaurin series occurs when x is largest, at the cutoff point of x < 1.3, the first few terms are 0.73233333333333350, -0.37129300000000015, 0.14940123095238103... so we're reducing by  a factor of 2 or more with each term, which is normally sufficient to ensure no cancellation error (at worst we loose the last bit).

---

## Comment 7

> Username: NAThompson  
> Created_at: 2020-08-14 12:54:42 UTC  
> Url: https://github.com/boostorg/math/pull/418#issuecomment-674060189  

bah . . . I was convinced it would work for `x < 1/2`; guess that shows how good my intuition is!

---
