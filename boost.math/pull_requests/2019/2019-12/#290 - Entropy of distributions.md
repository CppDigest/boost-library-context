# #290 Entropy of distributions. [Merged]

> Username: NAThompson  
> Created at: 2019-12-23 15:45:59 UTC  
> Updated at: 2019-12-31 01:54:02 UTC  
> Merged at: 2019-12-28 14:32:14 UTC  
> Closed at: 2019-12-28 14:32:14 UTC  
> Url: https://github.com/boostorg/math/pull/290  

There are still quite a few more distributions to do, but this is a good start.  
  
Should it be called  `differential_entropy` or is `entropy` just fine?  
  
Table from Wikipedia:  
  
https://en.wikipedia.org/wiki/Differential_entropy

---

## Comment 1

> Username: NAThompson  
> Created_at: 2019-12-24 15:37:05 UTC  
> Updated_at: 2019-12-24 15:38:18 UTC  
> Url: https://github.com/boostorg/math/pull/290#issuecomment-568768098  

@pabristow , @jzmaddock : Gimme your thoughts on this; looks like once the build cycles it'll basically be ready to go.  
  
Note that the bigger fish to fry is the Kullback-Liebler divergence. Will attempt to get that in a later PR.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2019-12-28 14:32:09 UTC  
> Url: https://github.com/boostorg/math/pull/290#issuecomment-569422000  

It seems like this is pretty uncontroversial, and I want to get working on the Kullback-Liebler divergence without merge commit issues, so I'm merging. Can revert if anyone objects.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2019-12-30 09:53:41 UTC  
> Url: https://github.com/boostorg/math/pull/290#issuecomment-569630133  

> There are still quite a few more distributions to do, but this is a good start.  
>   
> Should it be called `differential_entropy` or is `entropy` just fine?  
>   
> Table from Wikipedia:  
>   
> https://en.wikipedia.org/wiki/Differential_entropy  
  
This should be differential entropy, because the random variable (or more specifically, the PDF from which it comes) is continuous.  
The formula for (discrete) entropy and differential entropy are very similar, summation in one case, integration in the other, but a key difference in the result is that differential entropy can be negative.  
  
I'm not sure if Boost.Math already has a dependency on Boost.Units, but whether it does or not, I would consider giving the result the unit type "bits", so as to distinguish from the other units that (differential) entropy sometimes uses: nats,  
  
Cheers.

---

## Review 4 [Commented]

> Username: jeremy-murphy  
> Created_at: 2019-12-30 09:57:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/290#pullrequestreview-337052508  

📁 include/boost/math/distributions/cauchy.hpp

```diff
 353 |+    using std::log;
 354 |+    return log(2*constants::two_pi<RealType>()*dist.scale());
 355 |+ }
```

> Username: jeremy-murphy  
> Created_at: 2019-12-30 09:57:16 UTC  
> Updated_at: 2019-12-30 09:57:17 UTC  
> Url: https://github.com/boostorg/math/pull/290#discussion_r361943941  

I think integer `2` will implicitly convert to `double`, which could cause extra rounding when `RealType` is not `double`.  
I would be strict and write it as `RealType(2)`.

> Username: jzmaddock  
> Created_at: 2019-12-30 16:46:31 UTC  
> Url: https://github.com/boostorg/math/pull/290#discussion_r362037175  

Not in any normal situation I can think of, it's actually a conceptual requirement of Boost.Math that all real-valued types interoperate with integer literals, and we make plenty of use of that throughout the library.  Besides I can think of no situation where `2.0` (double precision) looses bits compared to `2`.  The only situation I can think of is if RealType isn't constructible or multipliable by `int` and has a lossy _implicit_ conversion operator to double.  
BTW in the multiprecision case, multiplying by an integer is likely to be significantly more efficient than constructing a temporary `RealType` and multiplying.

> Username: jeremy-murphy  
> Created_at: 2019-12-30 23:02:59 UTC  
> Url: https://github.com/boostorg/math/pull/290#discussion_r362117179  

> Not in any normal situation I can think of, it's actually a conceptual requirement of Boost.Math that all real-valued types interoperate with integer literals, and we make plenty of use of that throughout the library. Besides I can think of no situation where `2.0` (double precision) looses bits compared to `2`. The only situation I can think of is if RealType isn't constructible or multipliable by `int` and has a lossy _implicit_ conversion operator to double.  
  
What I'm thinking of is the so-called ["double rounding"](https://www.exploringbinary.com/double-rounding-errors-in-floating-point-conversions/) error, when `RealType` is `float`, then the extra conversion from `double` back down to `float` incurs an extra rounding.  
  
> BTW in the multiprecision case, multiplying by an integer is likely to be significantly more efficient than constructing a temporary `RealType` and multiplying.  
  
Yes, I understand that Boost.Math probably has a policy of being performance-friendly to multi-precision types, but even at the cost of accuracy to other floating point types? I'm not expecting you to flip a long-standing policy right now, and I'm sure that C++ being the language it is could solve all situations to everyone's liking (but with a lot more work).  
  
Surely this sort of small literal value performance penalty must arise frequently for multi-precision types? Could it not be solved in general with `constexpr` constructors or a small-object optimization?

> Username: NAThompson  
> Created_at: 2019-12-31 00:25:20 UTC  
> Url: https://github.com/boostorg/math/pull/290#discussion_r362126814  

Doesn't look like the double rounding problem happens on gcc:  
  
https://godbolt.org/z/m4q-vz

> Username: jeremy-murphy  
> Created_at: 2019-12-31 01:54:02 UTC  
> Url: https://github.com/boostorg/math/pull/290#discussion_r362134335  

Yes, it does look like clang and GCC optimize the integer literal away to a compile-time effect. Even integer non-literal values appeared to not have double rounding, so that's good.  
I assumed that the literal `int` would promote to `double` first.  
  
It's a different story when the literal is `2.0` and so actually has the `double` type. See for yourself, the `float` function now looks very inefficient. But that's not the actual situation here, so it's case closed for now. Something to think about though.  :)


---
