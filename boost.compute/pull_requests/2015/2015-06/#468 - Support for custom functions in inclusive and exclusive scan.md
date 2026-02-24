# #468 Support for custom functions in inclusive and exclusive scan [Merged]

> Username: jszuppe  
> Created at: 2015-06-19 18:48:39 UTC  
> Updated at: 2015-06-24 15:57:46 UTC  
> Merged at: 2015-06-24 15:30:06 UTC  
> Closed at: 2015-06-24 15:30:06 UTC  
> Url: https://github.com/boostorg/compute/pull/468  

This addresses issue https://github.com/boostorg/compute/issues/374.  
  
Now in inclusive and exclusive scan custom associative binary operator can be used instead of default addition operator. For exclusive scan also initial value can be set.   
  
I've updated docs for inclusive and exclusive scan.  
  
I've also added single argument constructor for vector types (`int2(1) == int2(1,1) == vector_type<int,2>(1)`, `int4(4) = int4(4, 4, 4, 4)  == vector_type<int,4>(1)`, etc.).

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-06-23 03:03:25 UTC  
> Updated_at: 2015-06-24 06:40:39 UTC  
> Url: https://github.com/boostorg/compute/pull/468#discussion_r33005886  

Use Boost.Compute's `ulong_` instead of `ulong` (which is not always available).

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-06-23 03:05:11 UTC  
> Updated_at: 2015-06-24 06:40:39 UTC  
> Url: https://github.com/boostorg/compute/pull/468#discussion_r33005938  

Same here, `ulong_`.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2015-06-23 03:05:56 UTC  
> Updated_at: 2015-06-24 06:40:39 UTC  
> Url: https://github.com/boostorg/compute/pull/468#discussion_r33005956  

Just for debugging?

---

## Comment 4

> Username: kylelutz  
> Created_at: 2015-06-23 03:11:13 UTC  
> Updated_at: 2015-06-24 06:40:39 UTC  
> Url: https://github.com/boostorg/compute/pull/468#discussion_r33006145  

We should probably mark this `explicit` to prevent unintentional conversions from scalars to vectors.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2015-06-23 03:12:08 UTC  
> Updated_at: 2015-06-24 06:40:39 UTC  
> Url: https://github.com/boostorg/compute/pull/468#discussion_r33006188  

Same here, probably mark this `explicit`.

---

## Comment 6

> Username: kylelutz  
> Created_at: 2015-06-23 03:12:50 UTC  
> Updated_at: 2015-06-24 06:40:39 UTC  
> Url: https://github.com/boostorg/compute/pull/468#discussion_r33006211  

You should pass the same `queue` argument here.

---

## Comment 7

> Username: kylelutz  
> Created_at: 2015-06-23 03:13:36 UTC  
> Updated_at: 2015-06-24 06:40:39 UTC  
> Url: https://github.com/boostorg/compute/pull/468#discussion_r33006243  

Same here, make sure you pass the `queue` argument so that the calls to `inclusive_scan` and the code to check the values are all on the same `command_queue`.

---

## Comment 8

> Username: kylelutz  
> Created_at: 2015-06-23 03:13:49 UTC  
> Updated_at: 2015-06-24 06:40:39 UTC  
> Url: https://github.com/boostorg/compute/pull/468#discussion_r33006252  

Pass `queue` here.

---

## Comment 9

> Username: kylelutz  
> Created_at: 2015-06-23 03:15:11 UTC  
> Url: https://github.com/boostorg/compute/pull/468#issuecomment-114339212  

Looks great! Left a few inline comments.

---

## Comment 10

> Username: jszuppe  
> Created_at: 2015-06-23 08:22:26 UTC  
> Url: https://github.com/boostorg/compute/pull/468#issuecomment-114403971  

Thanks for pointing out all these mistakes! I really don't know how - once again - I didn't delete that kernel source print, I thought I did :)   
  
Fixed, tested and pushed.

---

## Comment 11

> Username: kylelutz  
> Created_at: 2015-06-24 02:53:42 UTC  
> Updated_at: 2015-06-24 06:40:39 UTC  
> Url: https://github.com/boostorg/compute/pull/468#discussion_r33113781  

You should use `queue` here too (and for the rest of the algorithm calls in this test). In general, the tests should always use the context and command queue set up in `context_setup.hpp` instead of falling back to the default queue.

---

## Comment 12

> Username: kylelutz  
> Created_at: 2015-06-24 02:54:23 UTC  
> Url: https://github.com/boostorg/compute/pull/468#issuecomment-114708491  

Just a few places to add the queue parameter in the test and this should be good to go. Thanks!

---

## Comment 13

> Username: jszuppe  
> Created_at: 2015-06-24 06:37:24 UTC  
> Updated_at: 2015-06-24 06:40:39 UTC  
> Url: https://github.com/boostorg/compute/pull/468#discussion_r33120627  

OK, thanks!  
  
I've found out that the context or the queue parameter is missing in a few tests of other algorithms (e.g., test_accumulate.cpp, test product_int) . Should I fix them?

---

## Comment 14

> Username: jszuppe  
> Created_at: 2015-06-24 06:40:58 UTC  
> Url: https://github.com/boostorg/compute/pull/468#issuecomment-114749028  

Fixed. I also added the queue parameter in previous tests as it was missing there too.

---

## Comment 15

> Username: kylelutz  
> Created_at: 2015-06-24 15:30:16 UTC  
> Url: https://github.com/boostorg/compute/pull/468#issuecomment-114911604  

Merged! Thanks!

---
