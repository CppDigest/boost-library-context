# #173 adding shuffle.hpp:feature #60 [Closed]

> Username: Mageswaran1989  
> Created at: 2014-07-04 12:23:12 UTC  
> Updated at: 2014-11-05 18:42:35 UTC  
> Closed at: 2014-11-05 18:42:35 UTC  
> Url: https://github.com/boostorg/compute/pull/173  

Hi,  
I managed to pull things under shuffle.hpp.  
Now I get an error while using generate of uniform_int_distribution in shuffle.hpp (line 57).  
Need your help in fixing this error.  
  
If I define random generator local to shuffle_impl(), I am able to run it!

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-07-04 12:50:47 UTC  
> Url: https://github.com/boostorg/compute/pull/173#issuecomment-48040321  

[![Coverage Status](https://coveralls.io/builds/933389/badge)](https://coveralls.io/builds/933389)  
  
Coverage remained the same when pulling **a259ae8e12f56ae122825cf421b31133c475be79 on Mageswaran1989:shuffle-algorithm** into **bfe2bdd7f77644350d76f39a0be49feff79653a9 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-07-07 04:35:24 UTC  
> Url: https://github.com/boostorg/compute/pull/173#issuecomment-48140346  

Cool! Thanks for working on this!  
  
I've posted some comments inline. Also, it would be good to have a test suite for the new algorithm (potentially creating a copy of the input range, shuffling it, and then checking the result is valid with the `boost::compute::is_permutation()` algorithm).  
  
Also, what errors were you getting when using an external random number generator?

---

## Comment 3

> Username: Mageswaran1989  
> Created_at: 2014-07-07 13:55:06 UTC  
> Url: https://github.com/boostorg/compute/pull/173#issuecomment-48180741  

Hi,   
Error is   
terminate called after throwing an instance of 'boost::exception_detail::clone_implboost::exception_detail::error_info_injector<boost::compute::opencl_error >'  
  what():  Invalid Memory Object  
  
---  
  
Thanks for reminding and pointing that values are not being copied to the output at all.  
  
I feel with std::shuffle API requirements, Fisher–Yates_shuffle becomes more sequential.  
  
I was not able to map the logic given at http://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle  
to parallel, without using swap and a bit of sequential code.  
  
Either I am ending up with using "shuffle" API of OpenCL or using a mask to shuffle data within a work group, leaving passed random generator unused.  
  
Meanwhile thought of putting everything under shuffle.hpp and get feeling of boost::compute API design :)

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-07-07 14:40:59 UTC  
> Url: https://github.com/boostorg/compute/pull/173#issuecomment-48187030  

That `Invalid Memory Object` error is usually caused by mixing OpenCL buffers from different contexts. Make sure the context for the random number generator, the context for the input and output ranges, and the context for the command queue are all the same.  
  
And yeah, the Fischer-Yates shuffle is just a well-known sequential algorithm for shuffling. We don't need to actually use it, though our algorithm should perform the same high level operation (randomly shuffling an input range such that each output permutation is equally likely).

---
