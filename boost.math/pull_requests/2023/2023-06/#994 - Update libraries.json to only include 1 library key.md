# #994 Update libraries.json to only include 1 library key [Merged]

> Username: mborland  
> Created at: 2023-06-13 06:15:18 UTC  
> Updated at: 2023-06-26 08:03:27 UTC  
> Merged at: 2023-06-26 08:03:17 UTC  
> Closed at: 2023-06-26 08:03:17 UTC  
> Url: https://github.com/boostorg/math/pull/994  

Not too long ago this was asked about on the list, and revitalized on slack yesterday. @jzmaddock, @ckormanyos  and @NAThompson is there anything you want to add to the description? I hit the major sections that are in our readme.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2023-06-13 07:20:34 UTC  
> Url: https://github.com/boostorg/math/pull/994#issuecomment-1588692621  

> anything you want to add to the description?  
  
I've always been so impressed with seamless interaction (template scalability) of special functions and math tools like root finding. In fact their scalability to the extended width types in Multiprecision is a key fundamental excellence of Math.  
  
Is there any way to express this - in a more terse and clear way than my roundabout mention here?  
  
I think it's worth mentioning in the top-level summary.  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Comment 2

> Username: mborland  
> Created_at: 2023-06-13 12:06:06 UTC  
> Url: https://github.com/boostorg/math/pull/994#issuecomment-1589172967  

> I've always been so impressed with seamless interaction (template scalability) of special functions and math tools like root finding. In fact their scalability to the extended width types in Multiprecision is a key fundamental excellence of Math.  
  
I concur and have added a comment about it.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2023-06-13 12:08:43 UTC  
> Url: https://github.com/boostorg/math/pull/994#issuecomment-1589177108  

>> scalability ... is a key fundamental excellence of Math.  
  
> I concur and have added a comment about it.  
  
Awesome. I like the terse, clear way you expressed that. Thank you Matt

---

## Review 4 [Commented]

> Username: NAThompson  
> Created_at: 2023-06-13 15:03:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/994#pullrequestreview-1477416436  

📁 meta/libraries.json

```diff
   7 |         ],
   8 |-         "description": "Boost.Math includes several contributions in the domain of mathematics: The Greatest Common Divisor and Least Common Multiple library provides run-time and compile-time evaluation of the greatest common divisor (GCD) or least common multiple (LCM) of two integers. The Special Functions library currently provides eight templated special functions, in namespace boost. The Complex Number Inverse Trigonometric Functions are the inverses of trigonometric functions currently present in the C++ standard. Quaternions are a relative of complex numbers often used to parameterise rotations in three dimensional space. Octonions, like quaternions, are a relative of complex numbers.",
   8 |+         "description": "Boost.Math includes several contributions in the domain of mathematics: Floating Point Utilities, Specific Width Floating Point Types, Mathematical Constants, Statistical Distributions, Special Functions, Root Finding and Function Minimization, Polynomials and Rational Functions, Interpolation, Numerical Integration and Differentiation, Quaternions and Octonions. Many of these features are templated to support both built-in, and extended width types (e.g. Boost.Multiprecision)",
```

> Username: NAThompson  
> Created_at: 2023-06-13 15:03:13 UTC  
> Url: https://github.com/boostorg/math/pull/994#discussion_r1228288183  

Are the quaternions and octonions basically deprecated now? I believe they haven't been touched or used in a long time, and now Boost.QVM exists . . .

> Username: mborland  
> Created_at: 2023-06-13 15:09:16 UTC  
> Url: https://github.com/boostorg/math/pull/994#discussion_r1228299043  

I think that is fair. QVM is still getting regular updates.


---
