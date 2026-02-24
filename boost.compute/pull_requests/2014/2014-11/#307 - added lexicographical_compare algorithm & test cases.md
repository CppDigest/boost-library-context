# #307 added lexicographical_compare algorithm & test cases [Merged]

> Username: Mageswaran1989  
> Created at: 2014-11-17 20:11:41 UTC  
> Updated at: 2014-11-19 18:44:30 UTC  
> Merged at: 2014-11-19 18:44:10 UTC  
> Closed at: 2014-11-19 18:44:10 UTC  
> Url: https://github.com/boostorg/compute/pull/307  

Please review.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-11-18 06:10:57 UTC  
> Updated_at: 2014-11-19 18:19:54 UTC  
> Url: https://github.com/boostorg/compute/pull/307#discussion_r20487303  

`range1` and `range2` should be `const` here

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-11-18 06:11:42 UTC  
> Updated_at: 2014-11-19 18:19:54 UTC  
> Url: https://github.com/boostorg/compute/pull/307#discussion_r20487318  

for internal methods like this you don't need to use a default argument for queue, you can always depend on one being passed in.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-11-18 06:12:44 UTC  
> Updated_at: 2014-11-19 18:19:54 UTC  
> Url: https://github.com/boostorg/compute/pull/307#discussion_r20487344  

also pass `context` as a constructor argument here in case `queue`'s context is different from the default context.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-11-18 06:13:21 UTC  
> Updated_at: 2014-11-19 18:19:54 UTC  
> Url: https://github.com/boostorg/compute/pull/307#discussion_r20487362  

you can probably just delete this commented out line.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-11-18 06:14:24 UTC  
> Updated_at: 2014-11-19 18:19:54 UTC  
> Url: https://github.com/boostorg/compute/pull/307#discussion_r20487378  

you should pass `queue` as the last argument here so that the `any_of()` algorithm is also executed on the same command queue.

---

## Comment 6

> Username: kylelutz  
> Created_at: 2014-11-18 06:16:27 UTC  
> Updated_at: 2014-11-19 18:19:54 UTC  
> Url: https://github.com/boostorg/compute/pull/307#discussion_r20487412  

hmm, I don't think the pointers are necessary here, you can just directly pass `first1.get_buffer()` and `first2.get_buffer()` and `result_vector` (there's a `set_arg` overload which automatically does the right thing if you just pass in a `boost::compute::vector<T>`).

---

## Comment 7

> Username: kylelutz  
> Created_at: 2014-11-18 06:20:30 UTC  
> Updated_at: 2014-11-19 18:19:54 UTC  
> Url: https://github.com/boostorg/compute/pull/307#discussion_r20487483  

can you name this `"algorithm.lexicographical_compare"`? that way it lines up with the other algorithm tests and makes it easier to run just those (i.e. with "ctest -R algorithm.").

---

## Comment 8

> Username: kylelutz  
> Created_at: 2014-11-18 06:20:59 UTC  
> Updated_at: 2014-11-19 18:19:54 UTC  
> Url: https://github.com/boostorg/compute/pull/307#discussion_r20487492  

You should put your name here :-). And `2014`.

---

## Comment 9

> Username: kylelutz  
> Created_at: 2014-11-18 06:21:34 UTC  
> Updated_at: 2014-11-19 18:19:54 UTC  
> Url: https://github.com/boostorg/compute/pull/307#discussion_r20487502  

Could you add a doxygen comment to this? That way it will show up in the public documentation. See some of the other public algorithms for examples.

---

## Comment 10

> Username: kylelutz  
> Created_at: 2014-11-18 06:24:48 UTC  
> Url: https://github.com/boostorg/compute/pull/307#issuecomment-63427996  

Looks good. I left a few comments inline. Could you make those fixes and amend the commit? Also, could you add a line with `#include <boost/compute/algorithm/lexicographical_compare.hpp>` to `include/boost/compute/algorithm.hpp`. This header allows users to include all of the Boost.Compute algorithm headers by just doing `#include <boost/compute/algorithm.hpp>`. After that it should be good to merge. Thanks!

---

## Comment 11

> Username: coveralls  
> Created_at: 2014-11-18 07:02:56 UTC  
> Url: https://github.com/boostorg/compute/pull/307#issuecomment-63430384  

[![Coverage Status](https://coveralls.io/builds/1489234/badge)](https://coveralls.io/builds/1489234)  
  
Coverage remained the same when pulling **bbb2eb6577aa89bec41369d0a8ec8e2f2ef395c2 on Mageswaran1989:lexicographical_compare** into **3183effcb0e09766e5a1f120cd688af9447952db on kylelutz:develop**.

---

## Comment 12

> Username: kylelutz  
> Created_at: 2014-11-19 04:01:22 UTC  
> Updated_at: 2014-11-19 18:19:54 UTC  
> Url: https://github.com/boostorg/compute/pull/307#discussion_r20556756  

The test doesn't define any doxygen snippets so you should remove this (otherwise doxygen will have warnings/errors).

---

## Comment 13

> Username: kylelutz  
> Created_at: 2014-11-19 04:02:28 UTC  
> Url: https://github.com/boostorg/compute/pull/307#issuecomment-63589201  

Looks good. Left one note about the doxygen snippet. Also, could you squash this down to one commit? After that I'll merge it in.

---

## Comment 14

> Username: coveralls  
> Created_at: 2014-11-19 18:44:30 UTC  
> Url: https://github.com/boostorg/compute/pull/307#issuecomment-63690948  

[![Coverage Status](https://coveralls.io/builds/1498095/badge)](https://coveralls.io/builds/1498095)  
  
Coverage increased (+0.02%) when pulling **af7e38d2ced344e7c13b82744289164495294fa9 on Mageswaran1989:lexicographical_compare** into **3183effcb0e09766e5a1f120cd688af9447952db on kylelutz:develop**.

---
