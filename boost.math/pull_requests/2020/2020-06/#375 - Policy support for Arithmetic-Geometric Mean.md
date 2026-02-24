# #375 Policy support for Arithmetic-Geometric Mean [Closed]

> Username: evanmiller  
> Created at: 2020-06-22 14:17:15 UTC  
> Updated at: 2022-09-19 19:00:41 UTC  
> Closed at: 2022-09-19 19:00:41 UTC  
> Url: https://github.com/boostorg/math/pull/375  

As mentioned in #374, `agm` currently hangs when invoked with the `real_concept` type. This is because the function:  
  
    std::numeric_limits<real_concept>::epsilon()  
  
Returns zero, thus putting `agm` into an infinite loop.  
  
The current PR adapts the `agm` code to use floating-point policies, in particular `policies::get_epsilon<Real, Policy>()`, and introduces a second function signature to accept a `Policy` argument:  
  
```C++  
template<typename Real, typename Policy>  
Real agm(Real a, Real g, const Policy& pol);  
```  
  
The new signature is added to the documentation, and the tests are updated to use the Policy'ed version of `agm`. Tests are also added to cover the `real_concept` type to prove that the original AGM hang no longer occurs. Nothing in the logic of the algorithm is intended to be modified.  
  
Two other small changes are introduced here: first, a trivial implementation of `isnan()` for the `real_concept` type (necessary to get the new tests to compile and run), and second a small typo fix in one of the AGM test messages.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-06-22 15:46:40 UTC  
> Url: https://github.com/boostorg/math/pull/375#issuecomment-647607398  

So, the reason why I didn't do a policy before for this function was that 1) it doesn't throw exceptions, and 2) there is no need for argument promotion to achieve acceptable accuracy. I'm not convinced that compatibility with `real_concept` is sufficient motivation to transitively couple this header to so many others.  
  
However, @jzmaddock knows much more about the use of policies; so I will defer to his judgement on this.

---

## Comment 2

> Username: evanmiller  
> Created_at: 2020-06-22 16:19:06 UTC  
> Url: https://github.com/boostorg/math/pull/375#issuecomment-647625572  

@NAThompson Thank you for providing perspective on your design decision regarding policies.  
  
My immediate concern is/was: How can I call `agm` from other parts of the code (specifically elliptic integrals) and have all their `real_concept` tests pass? It may be as simple as persuading `std::numeric_limits<real_concept>::epsilon()` to return a non-zero result. For this PR I went with policies as they appeared to be ubiquitous in the (small amount of) code that I was examining, but I will readily admit my ignorance regarding the bigger picture.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2020-06-22 16:32:39 UTC  
> Url: https://github.com/boostorg/math/pull/375#issuecomment-647632926  

> My immediate concern is/was: How can I call agm from other parts of the code (specifically elliptic integrals) and have all their real_concept tests pass? I  
  
It's a good question, and I think the honest answer is "I don't know". For me personally, it only became clear that an `agm` was independently valuable apart from its use in the elliptic integrals very recently. So there is a lot of design, and redesign, that could reasonably be performed informed by this perspective.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2020-06-22 17:09:12 UTC  
> Url: https://github.com/boostorg/math/pull/375#issuecomment-647652077  

Let me try and explain the issue:  
  
There are some types - particularly those that have variable precision - where a numeric_limits specialization is problematic.  Basically there are a lot of compile time constants like `numeric_limits<>::digits` that you can't assign sane values to.  So.... a long time ago, I took the decision, not to require that a type specializes numeric_limits in order for it to be usable with the library.  Instead there are customization points like `boost::math::tools::epsilon<>()` which can be specialized to provide the similar support (of course they defer to numeric_limits when available).  At the same time the policy code was added - it does a lot of things I realise that - though the exception handling part isn't included in this case - but one (minor) use case in this situation is to intentionally reduce precision: as in "yes I know this is a double, but just calculate to 10 decimal places".  
  
Now for the big question: is this the right design?  I don't know.  And 15 years on there is still no "official" guidance that I know of for specializing numeric_limits for user-defined types.  Sometime's it's obvious what to do, other times not so much.  
  
BTW I realise this is going off topic, but it's a thorny issue that may need revisiting.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2020-06-22 17:47:42 UTC  
> Url: https://github.com/boostorg/math/pull/375#issuecomment-647679197  

@jzmaddock : I don't have any better ideas, so your design seems eminently reasonable.  
  
My own preference, at this point in time, is to make code that can be easily copy-pasted into other projects, just because that's the context in which I currently work: with ancient boost versions on (even new) CentOS systems. I think we can really reduce the motivation for this if we build a C++-20 module, but I was reading through the C++20 support of various compilers, and it's just not there yet. So I haven't expended any effort in this direction.

---

## Comment 6

> Username: pabristow  
> Created_at: 2020-06-23 08:25:55 UTC  
> Url: https://github.com/boostorg/math/pull/375#issuecomment-647991575  

There seems to be no 'official' interest in numeric_limits for user-defined types.  It is regarded as a niche issue and put into the 'too-difficult' drawer.  Probably this is wise, considering the any and much more important things to work on.  And the implementors are lagging even further behind.  It's really disappointing to find the a function like ceil still isn't constexpr.  I get the impression that policy works well, even if it is not popular because it just is complicated.  It is when using real_concepts with tests that trips us up. I don't know what to do about that. It is a "Doctor, Doctor, it hurts when I test with real_concepts. " Doctor "Don't do that?"

---

## Comment 7

> Username: NAThompson  
> Created_at: 2020-06-23 20:09:30 UTC  
> Url: https://github.com/boostorg/math/pull/375#issuecomment-648391201  

@evanmiller : My apologies that your first commit kinda stepped on a landmine! In reality most PRs are accepted with little fanfare. . . .

---

## Comment 8

> Username: evanmiller  
> Created_at: 2020-06-24 01:46:15 UTC  
> Url: https://github.com/boostorg/math/pull/375#issuecomment-648533051  

> @evanmiller : My apologies that your first commit kinda stepped on a landmine! In reality most PRs are accepted with little fanfare. . . .  
  
Quite all right, I'm happy to watch the debate unfold from the gallery...

---

## Comment 9

> Username: evanmiller  
> Created_at: 2020-09-04 18:36:22 UTC  
> Url: https://github.com/boostorg/math/pull/375#issuecomment-687314926  

Should I close this pull request or update it?

---
