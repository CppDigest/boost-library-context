# #213 exercise threads with log agm calculations [Merged]

> Username: ckormanyos  
> Created at: 2020-04-05 20:54:54 UTC  
> Updated at: 2020-08-04 19:23:04 UTC  
> Merged at: 2020-08-04 18:44:06 UTC  
> Closed at: 2020-08-04 18:44:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/213  

The example uses an AGM method to do a "from the ground up" calculation of logarithms. These are then compared with the value from multiprecision's log function.  
  
This example has been motivated in part by discussions in #211

---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2020-04-05 22:18:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/213#pullrequestreview-387867474  

📁 example/exercise_threading_log_agm.cpp

```diff
 210 |+     7573, 7577, 7583, 7589, 7591, 7603, 7607, 7621, 7639, 7643, 7649, 7669, 7673, 7681, 7687, 7691, 7699, 7703, 7717, 7723,
 211 |+     7727, 7741, 7753, 7757, 7759, 7789, 7793, 7817, 7823, 7829, 7841, 7853, 7867, 7873, 7877, 7879, 7883, 7901, 7907, 7919
 212 |+   }};
```

> Username: NAThompson  
> Created_at: 2020-04-05 22:18:59 UTC  
> Updated_at: 2020-07-22 05:52:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/213#discussion_r403764653  

This reminds me how badly we need a prime sieve!


---

## Comment 2

> Username: ckormanyos  
> Created_at: 2020-04-06 05:21:16 UTC  
> Updated_at: 2020-04-06 05:21:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/213#issuecomment-609571196  

> This reminds me how badly we need a prime sieve!  
  
Yes. Just for exemplary purposes, I will put in an elementary sieve.  
  
This example needs a lot more work such as separatnig the AGM work and reducing or eliminating the time of writing to `cout`. It's a starting point for discussions.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2020-04-06 08:01:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/213#issuecomment-609638289  

>> This reminds me how badly we need a prime sieve!  
> Yes. Just for exemplary purposes, I will put in an elementary sieve.  
  
Don't forget we have a big table of primes in Boost.Math already: https://www.boost.org/doc/libs/1_72_0/libs/math/doc/html/math_toolkit/number_series/primes.html

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2020-04-06 08:42:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/213#issuecomment-609657963  

>> Yes. Just for exemplary purposes, I will put in an elementary sieve.  
  
> Don't forget we have a big table of primes in Boost.Math already  
  
Ah, yes. I had forgotten. Thank you.  
Now using Boost.Math's table of 10,000 `constexpr` primes.  
The sieve is in there in a previous Git commit if we ever need it.  
  
Kind regards, Chris

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2020-04-06 09:48:07 UTC  
> Updated_at: 2020-04-06 09:49:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/213#issuecomment-609691271  

So I reduced the impact of writing to `cout`, and also changed the digits from 1001 to 501 (for all the bak ends), and also increased the number of function values to 10,000.  
  
I get the following timing (at the moment, I do not have an MPFR around).  
  
```  
// 501 digits, 10,000 values, 7 threads  
// backend              calculation_time_concurrent      calculation_time_sequential  
// cpp_dec_float                   12.3s                            47.8s  
// cpp_bin_float                   14.8s                            58.5s  
// gmp_float                        1.1s                             4.4s  
```  
  
On my benchmarks, there are no spurious preformances, as was mentioned in an off-site preliminary email communication.  
  
I will keep refining and also take a look at MPFR.  
  
A lot of the run-time might come from the log functions and MPFR has, if memory serves, super fast log. So that might be illuminating. I will also look at that.  
  
All in all, however, the thread exercise seems to be moving in the right direction.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2020-04-06 12:16:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/213#issuecomment-609758156  

Great!  Here's what I see:  
  
```  
cpp_dec_float:  
result_is_ok_concurrent: true, calculation_time_concurrent: 18.1s  
result_is_ok_sequential: true, calculation_time_sequential: 48.5s  
  
cpp_bin_float:  
result_is_ok_concurrent: true, calculation_time_concurrent: 18.7s  
result_is_ok_sequential: true, calculation_time_sequential: 50.4s  
  
gmp:  
result_is_ok_concurrent: true, calculation_time_concurrent: 3.3s  
result_is_ok_sequential: true, calculation_time_sequential: 12.4s  
  
mpfr:  
result_is_ok_concurrent: true, calculation_time_concurrent: 0.6s  
result_is_ok_sequential: true, calculation_time_sequential: 1.9s  
```

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2020-07-20 19:05:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/213#issuecomment-661277434  

@ckormanyos is this good to go?

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2020-07-21 04:58:34 UTC  
> Updated_at: 2020-07-21 05:06:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/213#issuecomment-661633643  

> @ckormanyos is this good to go?  
  
Thank you for asking.  
This example has been sitting idle for a few weeks.  
Can I get a few more days?  
  
- I would like to ensure that a merge from develop to this branch works.  
- Also, is there a place where i could write a (very few) couple of sentences about what this example is doing, its motivation and how it works?

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2020-07-21 05:07:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/213#issuecomment-661636085  

> merge from develop to this branch works...  
  
The merge from develop to this branch worked. I forgot unfortunately to tag with CI SKIP. Sorry.

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2020-07-22 05:54:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/213#issuecomment-662257239  

The code is ready.  
CI passed.  
And the comments are now sufficient.  
  
> @ckormanyos is this good to go?  
  
Yes.

---
