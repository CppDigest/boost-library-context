# #452 Catch divide by zero in GMP rational assignments [Merged]

> Username: ckormanyos  
> Created at: 2022-04-26 08:07:50 UTC  
> Updated at: 2022-04-30 17:38:53 UTC  
> Merged at: 2022-04-27 17:56:44 UTC  
> Closed at: 2022-04-27 17:56:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/452  

This is intended to handle #451  
  
Hi John (@jzmaddock) here is a first cut attempting to catch div-by-zero in GMP rational assignments/ctors.  
  
- Could you please take a look if I found all (and the right) places?  
- Also I was a bit concerned for handling the move assignments properly, and I'm unsure if you agree with what i did?  
- I wonder if we should add some explicit GMP-only tests in the arithmetic-tests? Such that these try and catch div by zero?  
  
I also handled that single remaining MSVC Level 4 warning here (although it's a bit off topic).

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2022-04-26 12:21:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/452#pullrequestreview-953282526  

📁 include/boost/multiprecision/gmp.hpp

```diff
1754 | }
1755 | 
1756 |+ inline bool eval_is_zero_move(gmp_int&& val)
```

> Username: jzmaddock  
> Created_at: 2022-04-26 12:21:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/452#discussion_r858646366  

Do we need a separate function for this - can't we just call eval_is_zero and rely on rvalue->lvalue conversion?

> Username: ckormanyos  
> Created_at: 2022-04-26 12:39:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/452#discussion_r858663204  

Agreed. Thanks John. I was unsure of that conversion.


---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2022-04-26 12:24:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/452#pullrequestreview-953286333  

📁 include/boost/multiprecision/gmp.hpp

```diff
2385 |    gmp_rational(const T& a, const U& b, typename std::enable_if<std::is_constructible<gmp_int, T>::value && std::is_constructible<gmp_int, U>::value>::type* = nullptr)
2386 |    {
2387 |+       if (b == static_cast<U>(0))
```

> Username: jzmaddock  
> Created_at: 2022-04-26 12:24:18 UTC  
> Updated_at: 2022-04-26 12:24:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/452#discussion_r858649045  

I don't think we have tests that would catch this, but I would worry about T and U being types which gmp_int is constructible from, but which do not have an `operator=(int)`, an example would be `mpz_t`.  
  
So I would be inclined to put this check after the construction of the gmp_int's below and use a call to eval_is_zero for the check.  Hope that makes sense!

> Username: ckormanyos  
> Created_at: 2022-04-26 12:40:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/452#discussion_r858663556  

I understand. This makes sense. i will tweak these and similar ones accordingly.


---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2022-04-26 12:24:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/452#pullrequestreview-953286925  

📁 include/boost/multiprecision/gmp.hpp

```diff
2398 |    gmp_rational(const gmp_int& a, const U& b, typename std::enable_if<std::is_constructible<gmp_int, U>::value>::type* = nullptr)
2399 |    {
2400 |+       if (b == static_cast<U>(0))
```

> Username: jzmaddock  
> Created_at: 2022-04-26 12:24:43 UTC  
> Updated_at: 2022-04-26 12:24:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/452#discussion_r858649429  

same here, and in a few places below.


---

## Comment 4

> Username: jzmaddock  
> Created_at: 2022-04-26 12:26:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/452#issuecomment-1109732928  

There's a couple of comments attached to source, but I'll work up some generic tests to go in test_arithmetic.hpp and then we can see what breaks ;)

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2022-04-26 13:27:13 UTC  
> Updated_at: 2022-04-26 14:58:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/452#issuecomment-1109797176  

Hi John (@jzmaddock) OK so the review comments above have been handled in 9afb2a809e04c953584f32b7a56631c6546c1916 which is now cycling and seems well.  
  
If this cycles green, could you please add a few relevant tests? After that (if it also goes green), we can wrap this one up maybe?

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2022-04-28 07:56:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/452#issuecomment-1111867875  

Tests are coming.... there are more failures with cpp_rational/rational_adaptor than I was expecting :(

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2022-04-28 08:14:05 UTC  
> Updated_at: 2022-04-28 08:14:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/452#issuecomment-1111883659  

> Tests are coming.... there are more failures with cpp_rational/rational_adaptor than I was expecting  
  
Understood. Thanks John!  
  
Sorry, I think, if I might have moved too quickly forward on merging. I believe we will need a new PR for adding the tests...  
  
I will wait on any/all further activities until we resolve this phenomenon.

---
