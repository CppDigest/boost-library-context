# #380 Remove boost.random dependency [Merged]

> Username: mborland  
> Created at: 2021-10-10 11:04:32 UTC  
> Updated at: 2021-11-26 10:17:13 UTC  
> Merged at: 2021-11-26 09:15:14 UTC  
> Closed at: 2021-11-26 09:15:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/380  

In standalone mode the functionality of `miller_rabin_test` is reduced to only those types that are compatible with `std::uniform_int_distribution`.

---

## Comment 1

> Username: mborland  
> Created_at: 2021-10-11 07:08:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/380#issuecomment-939747199  

@jzmaddock, @ckormanyos, and @NAThompson I believe this is good for review. Is this functionality that is needed in standalone or is it safe to exclude it like I have? Porting from Boost.Random would be possible if needed, but it is definitely vintage code that has a lot of dependencies that come with the age.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-10-11 07:20:01 UTC  
> Updated_at: 2021-10-11 07:20:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/380#issuecomment-939756943  

> In standalone mode the functionality of `miller_rabin_test` is reduced to only those types that are compatible with `std::uniform_int_distribution`  
  
> Porting from Boost.Random would be possible if needed,   
  
I think it will be quite a relief to remove the dependency on Boost.Random.  
  
I'm not sure, but thinking out loud here. There are tests and at least one example that require wide integer types for testing Miller-Rabin. But I think basically these tests simply need a random uniform bit-stream genertor for N bits. With just a few lines of C++11 code, it should be possible to make or fashion a little utility from an integer distribution on the range (0,1) for N bits that could then be used in these tests and examples --- standalone or not. I can help if this is thought to be feasible.

---

## Comment 3

> Username: mborland  
> Created_at: 2021-10-13 18:02:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/380#issuecomment-942577320  

In this latest set of commits I have ported `uniform_int_distribution` and modernized the implementation to use C++11 internally removing any external dependencies. This breaks the dependency on Boost.Random with or without standalone mode.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2021-10-14 04:06:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/380#issuecomment-942927002  

> have ported `uniform_int_distribution` and modernized the implementation to use C++11 internally removing any external dependencies. This breaks the dependency on Boost.Random with or without standalone mode.  
  
I think it is a good detail/utility that removes a rather heavy dependency.

---

## Comment 5

> Username: mborland  
> Created_at: 2021-10-14 16:53:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/380#issuecomment-943540836  

CI on this one is clean so pending review it should be good to go.

---

## Review 6 [Commented]

> Username: jzmaddock  
> Created_at: 2021-10-23 11:38:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/380#pullrequestreview-787384464  

📁 performance/miller_rabin_performance.cpp

```diff
  37 | 
  39 |-    independent_bits_engine<mt11213b, 256, mpz_int> gen;
  38 |+    std::mt19937 gen;
```

> Username: jzmaddock  
> Created_at: 2021-10-23 11:38:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/380#discussion_r734960340  

Hold on a second, this changes things quite a bit: previously we were generating 256 bit values via multiple calls to the engine, now we're generating 32-bit values to test!

> Username: jzmaddock  
> Created_at: 2021-10-23 11:39:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/380#discussion_r734960398  

BTW I see no huge need to remove Boost.Random from tests?

> Username: ckormanyos  
> Created_at: 2021-10-23 11:52:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/380#discussion_r734961557  

> previously we were generating 256 bit values via multiple calls to the engine, now we're generating 32-bit values to test!  
  
Sorry. I had missed that point. We definitely need wide, non-built-in randoms for the Miller-Rabin tests.  
  
> I see no huge need to remove Boost.Random from tests  
  
I agree. Only the _include_ headers need freedom from dependency. The tests already include Boost.Test and a whole slough of other things a lot more _heavy_ than Boost.Random. I think I might have missed the point on this post slightly.


---

## Review 7 [Commented]

> Username: jzmaddock  
> Created_at: 2021-10-23 11:40:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/380#pullrequestreview-787384541  

📁 performance/miller_rabin_performance.hpp

```diff
  60 |-    independent_bits_engine<mt11213b, 256, IntType> gen;
  59 |+    std::random_device rd;
  60 |+    std::mt19937 gen(rd());
```

> Username: jzmaddock  
> Created_at: 2021-10-23 11:40:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/380#discussion_r734960442  

As above, we're gone from 256 bit values to 32 bit test values.


---

## Review 8 [Commented]

> Username: jzmaddock  
> Created_at: 2021-10-23 11:41:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/380#pullrequestreview-787384630  

📁 test/test_miller_rabin.cpp

```diff
  37 |- 
  38 |-    independent_bits_engine<mt11213b, test_bits, test_type> gen;
  33 |+    std::random_device rd;
```

> Username: jzmaddock  
> Created_at: 2021-10-23 11:41:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/380#discussion_r734960560  

Again, I think this breaks the purpose of the test.


---

## Comment 9

> Username: mborland  
> Created_at: 2021-11-24 19:34:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/380#issuecomment-978166023  

This is clean with the requested changes. Only failure is internal to the CI.

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2021-11-26 10:17:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/380#issuecomment-979851963  

Hi @mborland and @jzmaddock this is cool.  
  
In another project I just experienced a problem because my project had (falsely) relied on the parasitic inclusion of some `Boost.Random` headers via the inclusion of `<boost/multiprecision/miller_rabin.hpp>`.  
  
Although this is/was a problem in my particular project, it wouldn't surprise me if others experience such things in 1.79 down the road. Is it important for others? Can we announce explicitly in changes, as I think a few others might run into this nuance.

---
