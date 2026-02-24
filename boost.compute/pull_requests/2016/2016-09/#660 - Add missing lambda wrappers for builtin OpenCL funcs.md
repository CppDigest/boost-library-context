# #660 Add missing lambda wrappers for builtin OpenCL funcs [Merged]

> Username: jszuppe  
> Created at: 2016-09-20 16:13:30 UTC  
> Updated at: 2016-10-20 09:02:57 UTC  
> Merged at: 2016-10-19 04:10:01 UTC  
> Closed at: 2016-10-19 04:10:01 UTC  
> Url: https://github.com/boostorg/compute/pull/660  

I added a few missing lambda wrappers for OpenCL builtin function (see https://github.com/boostorg/compute/issues/659), but I noticed that a lot more is missing. @kylelutz, did you skip them on purpose or should I add all of them?

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-09-20 17:28:18 UTC  
> Url: https://github.com/boostorg/compute/pull/660#issuecomment-248372053  

[![Coverage Status](https://coveralls.io/builds/7971489/badge)](https://coveralls.io/builds/7971489)  
  
Coverage increased (+0.004%) to 83.342% when pulling **90d106e70c253656d2d60db935c26c32eaf5d72f on haahh:pr_lambda_funcs** into **86211062a771ab0b494116b4223b63a292ad7d11 on boostorg:develop**.

---

## Comment 2

> Username: coveralls  
> Created_at: 2016-09-22 14:02:39 UTC  
> Url: https://github.com/boostorg/compute/pull/660#issuecomment-248912375  

[![Coverage Status](https://coveralls.io/builds/8005594/badge)](https://coveralls.io/builds/8005594)  
  
Coverage increased (+0.009%) to 83.346% when pulling **b3d8e49d09ad81218e3e7010cbf7e11ea1e8edad on haahh:pr_lambda_funcs** into **86211062a771ab0b494116b4223b63a292ad7d11 on boostorg:develop**.

---

## Review 3 [Commented]

> Username: kenba  
> Created_at: 2016-09-23 13:02:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compute/pull/660#pullrequestreview-1323921  

📁 include/boost/compute/lambda/functional.hpp

```diff
 377 |+ BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION(add_sat)
 378 |+ BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION(hadd)
 379 |+ BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION(hradd)
```

> Username: kenba  
> Created_at: 2016-09-23 13:02:40 UTC  
> Updated_at: 2016-09-24 20:35:22 UTC  
> Url: https://github.com/boostorg/compute/pull/660#discussion_r80239492  

6.13.3 Integer functions, 4th function is:`rhadd` not `hradd`


---

## Review 4 [Commented]

> Username: kenba  
> Created_at: 2016-09-23 13:11:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compute/pull/660#pullrequestreview-1325238  

📁 include/boost/compute/lambda/functional.hpp

```diff
 412 | BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(exp2)
 413 | BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(exp10)
 414 |+ BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(fabs)
```

> Username: kenba  
> Created_at: 2016-09-23 13:11:16 UTC  
> Updated_at: 2016-09-24 20:35:22 UTC  
> Url: https://github.com/boostorg/compute/pull/660#discussion_r80240862  

6.13.2 Math functions, `expm1` is missing


---

## Review 5 [Commented]

> Username: kenba  
> Created_at: 2016-09-23 13:14:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compute/pull/660#pullrequestreview-1325687  

📁 include/boost/compute/lambda/functional.hpp

```diff
 419 |+ BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION(fmin)
 420 |+ BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION(fmod)
 421 |+ BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION(hypot)
```

> Username: kenba  
> Created_at: 2016-09-23 13:14:32 UTC  
> Updated_at: 2016-09-24 20:35:22 UTC  
> Url: https://github.com/boostorg/compute/pull/660#discussion_r80241362  

6.13.2 Math functions: `fract` and `frexp` are missing


---

## Review 6 [Commented]

> Username: kenba  
> Created_at: 2016-09-23 13:20:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compute/pull/660#pullrequestreview-1326579  

📁 include/boost/compute/lambda/functional.hpp

```diff
 429 |+ BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(logb)
 430 |+ BOOST_COMPUTE_LAMBDA_WRAP_TERNARY_FUNCTION(mad)
 431 |+ BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION(nextafter)
```

> Username: kenba  
> Created_at: 2016-09-23 13:20:50 UTC  
> Updated_at: 2016-09-24 20:35:22 UTC  
> Url: https://github.com/boostorg/compute/pull/660#discussion_r80242385  

6.13.2 Math functions: `maxmag`, `minmag`, `modf` & `nan` are missing.


---

## Review 7 [Commented]

> Username: kenba  
> Created_at: 2016-09-23 13:23:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compute/pull/660#pullrequestreview-1327068  

📁 include/boost/compute/lambda/functional.hpp

```diff
 434 | BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION(powr)
 435 |+ BOOST_COMPUTE_LAMBDA_WRAP_BINARY_FUNCTION(remainder)
 436 |+ BOOST_COMPUTE_LAMBDA_WRAP_UNARY_FUNCTION_T(rint)
```

> Username: kenba  
> Created_at: 2016-09-23 13:23:32 UTC  
> Updated_at: 2016-09-24 20:35:22 UTC  
> Url: https://github.com/boostorg/compute/pull/660#discussion_r80242897  

6.13.2 Math function: `remquo` is missing


---

## Review 8 [Changes requested]

> Username: kenba  
> Created_at: 2016-09-23 13:31:47 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/compute/pull/660#pullrequestreview-1328419  

Excellent work Jakub. It was a much bigger change than I was expecting. I particularly liked that grouped and ordered the functions as in the OpenCL spec, it made reviewing much easier.  
  
I only reviewed the changes for completeness against the OpenCL language spec. I've put comments where I think that functions are missing.   
  
Also what about the optional `half_*` and `native_*` functions. Should they also be included?

---

## Comment 9

> Username: jszuppe  
> Created_at: 2016-09-23 14:17:48 UTC  
> Url: https://github.com/boostorg/compute/pull/660#issuecomment-249205142  

Thanks for the comments, I'll look at them later today.

---

## Comment 10

> Username: jszuppe  
> Created_at: 2016-09-24 15:26:39 UTC  
> Url: https://github.com/boostorg/compute/pull/660#issuecomment-249370425  

I just need to add `nan` and everything will be ready.

---

## Review 11 [Commented]

> Username: kylelutz  
> Created_at: 2016-09-24 15:47:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compute/pull/660#pullrequestreview-1437062  

📁 include/boost/compute/detail/vector_type_producer.hpp

```diff
  32 |+ /// \endcode
  33 |+ template<class Scalar, size_t N>
  34 |+ struct vector_type_producer
```

> Username: kylelutz  
> Created_at: 2016-09-24 15:47:57 UTC  
> Updated_at: 2016-09-24 20:35:22 UTC  
> Url: https://github.com/boostorg/compute/pull/660#discussion_r80363263  

Isn't this the same as `make_vector_type<T, N>` from `<boost/compute/type_traits/make_vector_type.hpp>`?

> Username: jszuppe  
> Created_at: 2016-09-24 16:09:00 UTC  
> Updated_at: 2016-09-24 20:35:22 UTC  
> Url: https://github.com/boostorg/compute/pull/660#discussion_r80363650  

Oh, yes, you're right. I couldn't find it. I'll remove it.


---

## Comment 12

> Username: kylelutz  
> Created_at: 2016-09-24 15:52:27 UTC  
> Url: https://github.com/boostorg/compute/pull/660#issuecomment-249371802  

Thanks for adding the rest of these! Yeah, I don't think I skipped them on purpose, just never got around to adding them.

---

## Comment 13

> Username: jszuppe  
> Created_at: 2016-09-24 16:24:40 UTC  
> Updated_at: 2016-09-24 16:26:05 UTC  
> Url: https://github.com/boostorg/compute/pull/660#issuecomment-249373456  

I'll add `nan`, and maybe those `native_` and `half_` functions later today and then this pull request will be ready.

---

## Comment 14

> Username: coveralls  
> Created_at: 2016-09-24 16:35:12 UTC  
> Url: https://github.com/boostorg/compute/pull/660#issuecomment-249373994  

[![Coverage Status](https://coveralls.io/builds/8035926/badge)](https://coveralls.io/builds/8035926)  
  
Coverage increased (+0.01%) to 83.351% when pulling **070575022816e1dd13250aafac1af09ffba0359c on haahh:pr_lambda_funcs** into **86211062a771ab0b494116b4223b63a292ad7d11 on boostorg:develop**.

---

## Comment 15

> Username: coveralls  
> Created_at: 2016-09-24 17:31:22 UTC  
> Url: https://github.com/boostorg/compute/pull/660#issuecomment-249377064  

[![Coverage Status](https://coveralls.io/builds/8036221/badge)](https://coveralls.io/builds/8036221)  
  
Coverage increased (+0.01%) to 83.351% when pulling **070575022816e1dd13250aafac1af09ffba0359c on haahh:pr_lambda_funcs** into **86211062a771ab0b494116b4223b63a292ad7d11 on boostorg:develop**.

---

## Comment 16

> Username: coveralls  
> Created_at: 2016-09-24 22:11:20 UTC  
> Url: https://github.com/boostorg/compute/pull/660#issuecomment-249390500  

[![Coverage Status](https://coveralls.io/builds/8037421/badge)](https://coveralls.io/builds/8037421)  
  
Coverage increased (+0.01%) to 83.351% when pulling **e25bb7d99d00472c3f869937b0703adb48b16a8a on haahh:pr_lambda_funcs** into **86211062a771ab0b494116b4223b63a292ad7d11 on boostorg:develop**.

---

## Comment 17

> Username: kylelutz  
> Created_at: 2016-10-19 04:10:09 UTC  
> Url: https://github.com/boostorg/compute/pull/660#issuecomment-254705694  

Merged! Thanks for adding all these!

---

## Comment 18

> Username: kenba  
> Created_at: 2016-10-20 09:01:05 UTC  
> Url: https://github.com/boostorg/compute/pull/660#issuecomment-255049507  

I've just tested the merged changes in the develop branch against the original failing code for issue #659 and I'm delighted to report that it's definitely fixed the issue for normalize and atan2.  
  
Thanks again Jakub.

---

## Comment 19

> Username: jszuppe  
> Created_at: 2016-10-20 09:02:57 UTC  
> Url: https://github.com/boostorg/compute/pull/660#issuecomment-255049927  

Glad, I could help.

---
