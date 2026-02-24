# #149 added example/matrix_multiplication.cpp [Closed]

> Username: Mageswaran1989  
> Created at: 2014-06-20 06:59:05 UTC  
> Updated at: 2014-12-01 04:05:35 UTC  
> Closed at: 2014-12-01 04:05:35 UTC  
> Url: https://github.com/boostorg/compute/pull/149  

Hi,  
Please review the example/matrix_multiplication.cpp  
  
For some vague reason libboost_system was not getting detected, so added "system" in example/CMakeLists.txt

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-06-20 07:28:14 UTC  
> Url: https://github.com/boostorg/compute/pull/149#issuecomment-46651853  

[![Coverage Status](https://coveralls.io/builds/886754/badge)](https://coveralls.io/builds/886754)  
  
Coverage remained the same when pulling **634a448688fdd5166f7db3b260ab2149b70bd118 on Mageswaran1989:adding-matrix_multiplication-example** into **4abb85705c07d13d87fe76fa93d921bc698e4d76 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-06-20 15:36:33 UTC  
> Updated_at: 2014-06-21 10:01:20 UTC  
> Url: https://github.com/boostorg/compute/pull/149#discussion_r14025833  

is this still needed?

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-06-20 15:37:53 UTC  
> Updated_at: 2014-06-21 10:01:20 UTC  
> Url: https://github.com/boostorg/compute/pull/149#discussion_r14025916  

some of these headers don't seem to be used/needed (like `wait_list.hpp` and `user_event.hpp` and `platform.hpp`). it's good to only include the headers that are used.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-06-20 15:38:19 UTC  
> Updated_at: 2014-06-21 10:01:20 UTC  
> Url: https://github.com/boostorg/compute/pull/149#discussion_r14025942  

i don't think this is necessary.

---

## Comment 5

> Username: Mageswaran1989  
> Created_at: 2014-06-20 15:51:56 UTC  
> Url: https://github.com/boostorg/compute/pull/149#issuecomment-46694369  

removed extra headers......

---

## Comment 6

> Username: coveralls  
> Created_at: 2014-06-20 16:24:29 UTC  
> Url: https://github.com/boostorg/compute/pull/149#issuecomment-46697995  

[![Coverage Status](https://coveralls.io/builds/888289/badge)](https://coveralls.io/builds/888289)  
  
Coverage remained the same when pulling **e79b6d9eb514ef8ee1b282708027942f3c25eab2 on Mageswaran1989:adding-matrix_multiplication-example** into **4abb85705c07d13d87fe76fa93d921bc698e4d76 on kylelutz:develop**.

---

## Comment 7

> Username: kylelutz  
> Created_at: 2014-06-21 02:07:05 UTC  
> Url: https://github.com/boostorg/compute/pull/149#issuecomment-46741887  

So I don't think this should need to be linked with `boost_system`. What kind of errors did you get when you didn't add `system` to the `CMakeLists.txt`?

---

## Comment 8

> Username: Mageswaran1989  
> Created_at: 2014-11-05 18:46:09 UTC  
> Url: https://github.com/boostorg/compute/pull/149#issuecomment-61859534  

Please review

---

## Comment 9

> Username: kylelutz  
> Created_at: 2014-11-06 04:07:23 UTC  
> Url: https://github.com/boostorg/compute/pull/149#discussion_r19925537  

is this really necessary? can't you just include `<cstdint>`?

---

## Comment 10

> Username: kylelutz  
> Created_at: 2014-11-06 04:09:36 UTC  
> Updated_at: 2014-11-06 04:09:43 UTC  
> Url: https://github.com/boostorg/compute/pull/149#discussion_r19925588  

While the comments are nice, we don't really need doxygen-style markup for them in the examples (doxygen only gets run over the header files in `<boost/compute/...>`). Just a nice, human-readable description of the function would be ideal.

---

## Comment 11

> Username: kylelutz  
> Created_at: 2014-11-06 04:10:25 UTC  
> Url: https://github.com/boostorg/compute/pull/149#discussion_r19925612  

couldn't we just use `std::fill_n()`?

---

## Comment 12

> Username: kylelutz  
> Created_at: 2014-11-06 04:31:09 UTC  
> Url: https://github.com/boostorg/compute/pull/149#discussion_r19926101  

I don't think we should special-case the 8x8 matrix-multiplication. Instead, 8x8 should be the default value for `size` and it should just use the same code as above.

---

## Comment 13

> Username: kylelutz  
> Created_at: 2014-11-06 04:32:14 UTC  
> Url: https://github.com/boostorg/compute/pull/149#discussion_r19926125  

I don't think we need this, the help message should be sufficient for indicating how to run the example program.

---

## Comment 14

> Username: kylelutz  
> Created_at: 2014-11-06 04:41:49 UTC  
> Updated_at: 2014-11-06 04:42:28 UTC  
> Url: https://github.com/boostorg/compute/pull/149#discussion_r19926322  

does all this need to be in a big try-catch block? (the default exception handler should already do the job of printing out the error string when an uncaught exception is thrown)

---

## Comment 15

> Username: kylelutz  
> Created_at: 2014-11-06 04:43:41 UTC  
> Url: https://github.com/boostorg/compute/pull/149#issuecomment-61926901  

This is a useful example but I'd like to try to keep it more simple. Ideally an example program would demonstrate one single concept/function as clearly and concisely as possible. This helps potential new users focus on the essential components that they need and allows for them to easily extend the example to fit their specific use-case. If the example code becomes to large these things become more difficult.  
  
For this example, I think it would be better to just implement one of the matrix-multiplication kernels and show how to code, compile, and execute that. Ideally this would be implemented in ~100-200 lines of code.  
  
If you're interested in developing and benchmarking different matrix-multiplication implementations for the host and the GPU then I'd add them under the `perf/` directory (i.e. `perf_matrix_multiplication` and a `perf_host_matrix_multiplication`). That would be the perfect place for benchmark programs which compare and output timing information for these routines.  
  
Thanks for working on this!

---

## Comment 16

> Username: Mageswaran1989  
> Created_at: 2014-11-06 04:51:13 UTC  
> Url: https://github.com/boostorg/compute/pull/149#issuecomment-61927319  

yup got it... I will try to make it clear and concise

---
