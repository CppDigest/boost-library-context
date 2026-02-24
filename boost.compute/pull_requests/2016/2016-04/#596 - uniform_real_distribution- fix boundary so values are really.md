# #596 uniform_real_distribution: fix boundary so values are really [a,b) instead of [a,b] [Closed]

> Username: jagerman  
> Created at: 2016-04-24 19:21:56 UTC  
> Updated at: 2016-04-24 22:36:25 UTC  
> Closed at: 2016-04-24 19:25:04 UTC  
> Url: https://github.com/boostorg/compute/pull/596  

Use the same nextafter approach from boost::compute::random::normal_distribution to eliminate the possibility of getting a draw equal to b, so that we really do produce [a,b) values, as currently documented (and desired).

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-04-24 19:23:57 UTC  
> Url: https://github.com/boostorg/compute/pull/596#issuecomment-214025393  

Just 15 minutes ago I pushed the same PR. Well without the assertion `BOOST_ASSERT(a < b);` but with additional test.

---

## Comment 2

> Username: jagerman  
> Created_at: 2016-04-24 19:25:04 UTC  
> Url: https://github.com/boostorg/compute/pull/596#issuecomment-214025509  

Yeah, I just noticed that.  Closing this one.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2016-04-24 19:26:21 UTC  
> Url: https://github.com/boostorg/compute/pull/596#issuecomment-214025584  

I'll add the assert. It's a good idea.

---

## Comment 4

> Username: coveralls  
> Created_at: 2016-04-24 22:36:14 UTC  
> Url: https://github.com/boostorg/compute/pull/596#issuecomment-214053921  

[![Coverage Status](https://coveralls.io/builds/5912318/badge)](https://coveralls.io/builds/5912318)  
  
Coverage increased (+0.0009%) to 88.792% when pulling **81b826d42a1568b754caa183d85aca84ae7fbafa on jagerman:develop** into **48217d2918d64ac84c13822384c67c4edcad5481 on boostorg:develop**.

---
