# #113 Set intersection [Merged]

> Username: roshanrags  
> Created at: 2014-05-15 21:59:52 UTC  
> Updated at: 2014-05-20 21:00:52 UTC  
> Merged at: 2014-05-17 18:39:44 UTC  
> Closed at: 2014-05-17 18:39:44 UTC  
> Url: https://github.com/boostorg/compute/pull/113  

Added new kernels, algorithm and test for `set_intersection`

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-05-15 22:28:45 UTC  
> Updated_at: 2014-05-20 20:51:15 UTC  
> Url: https://github.com/boostorg/compute/pull/113#issuecomment-43272858  

[![Coverage Status](https://coveralls.io/builds/775405/badge)](https://coveralls.io/builds/775405)  
  
Coverage increased (+0.08%) when pulling **663579c385ccfcaceea31bb57d9552f481e87b01 on roshanr95:set_intersection** into **2fffd98c904bcbf6b60a20abe6c08d6fbf7675dd on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-05-16 05:11:35 UTC  
> Updated_at: 2014-05-17 15:30:14 UTC  
> Url: https://github.com/boostorg/compute/pull/113#discussion_r12725406  

debugging code? should be removed.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-05-16 05:11:46 UTC  
> Updated_at: 2014-05-17 15:30:14 UTC  
> Url: https://github.com/boostorg/compute/pull/113#discussion_r12725409  

debugging code? should be removed.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-05-16 05:11:53 UTC  
> Updated_at: 2014-05-17 15:30:14 UTC  
> Url: https://github.com/boostorg/compute/pull/113#discussion_r12725411  

debugging code? should be removed.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-05-16 05:17:30 UTC  
> Updated_at: 2014-05-17 15:30:14 UTC  
> Url: https://github.com/boostorg/compute/pull/113#discussion_r12725453  

instead of using the vector's `operator[]` (which internally creates a new `command_queue` to use just for the operation), it would be better just to say `fill_n(counts.begin() - end, 1, 0, queue)` (or something similar with `copy()`). This is more efficient and removes the need for the `queue.finish()` call below.  
  
just fyi, I'm thinking about deprecating the `operator[]` in favor of explicitly copying values (via `copy()` or `fill()` or `vector<T>::assign()`) which doesn't have to create it's own queue internally and removes race conditions between operations performed on different command queues (which would eliminate the need to call `queue.finish()` after each operation to be safe).

---

## Comment 6

> Username: kylelutz  
> Created_at: 2014-05-16 05:18:02 UTC  
> Updated_at: 2014-05-17 15:30:14 UTC  
> Url: https://github.com/boostorg/compute/pull/113#discussion_r12725460  

can all this commented out code be removed?

---

## Comment 7

> Username: kylelutz  
> Created_at: 2014-05-16 05:19:18 UTC  
> Url: https://github.com/boostorg/compute/pull/113#issuecomment-43298258  

Looks good! The tests pass on my machine. Left a few inline comments. After those are addressed it should be good to merge.

---

## Comment 8

> Username: roshanrags  
> Created_at: 2014-05-16 09:48:59 UTC  
> Url: https://github.com/boostorg/compute/pull/113#issuecomment-43314829  

Oh sorry for the kind of half-baked version, forgot to mention that I actually wanted you to test it on your machine since I had issues with it passing on my machine and failing on travis. Anyway good to hear that it is working on your setup as well. Will make a final version fit for merging soon...

---

## Comment 9

> Username: roshanrags  
> Created_at: 2014-05-16 16:01:04 UTC  
> Url: https://github.com/boostorg/compute/pull/113#issuecomment-43348563  

Done :)

---

## Comment 10

> Username: coveralls  
> Created_at: 2014-05-16 16:02:33 UTC  
> Url: https://github.com/boostorg/compute/pull/113#issuecomment-43348712  

[![Coverage Status](https://coveralls.io/builds/777626/badge)](https://coveralls.io/builds/777626)  
  
Coverage remained the same when pulling **c522d9a3373ea5bd2798b923f81f7fa115968fe5 on roshanr95:set_intersection** into **2fffd98c904bcbf6b60a20abe6c08d6fbf7675dd on kylelutz:develop**.

---

## Comment 11

> Username: kylelutz  
> Created_at: 2014-05-16 20:30:01 UTC  
> Updated_at: 2014-05-17 15:30:14 UTC  
> Url: https://github.com/boostorg/compute/pull/113#discussion_r12758149  

this shouldn't be needed (calling `clFinish()` can be expensive)

---

## Comment 12

> Username: kylelutz  
> Created_at: 2014-05-16 20:32:18 UTC  
> Updated_at: 2014-05-17 15:30:14 UTC  
> Url: https://github.com/boostorg/compute/pull/113#discussion_r12758286  

so i don't think you need to store this as a string (and removing it makes the `atoi()` usage below unnecessary). you can directly insert `int`s into `meta_kernel` and it will do the stringification for you preventing any unnecessary string operations in the other places you use `tile_size`.

---

## Comment 13

> Username: kylelutz  
> Created_at: 2014-05-16 20:32:41 UTC  
> Updated_at: 2014-05-17 15:30:14 UTC  
> Url: https://github.com/boostorg/compute/pull/113#discussion_r12758300  

see my comment below about the other `tile_size`

---

## Comment 14

> Username: kylelutz  
> Created_at: 2014-05-16 20:33:23 UTC  
> Url: https://github.com/boostorg/compute/pull/113#issuecomment-43376261  

Cool! Left a few more comments. I'm away right now but I should be able to test this on my AMD machine early next week.

---

## Comment 15

> Username: roshanrags  
> Created_at: 2014-05-16 21:46:04 UTC  
> Updated_at: 2014-05-17 15:30:14 UTC  
> Url: https://github.com/boostorg/compute/pull/113#discussion_r12761132  

It is needed to determine the number of elements in the intersection. Without waiting for the queue to finish, how would I be able to return an iterator pointing to end of intersection?

---

## Comment 16

> Username: kylelutz  
> Created_at: 2014-05-16 22:24:50 UTC  
> Updated_at: 2014-05-17 15:30:14 UTC  
> Url: https://github.com/boostorg/compute/pull/113#discussion_r12762157  

ahh, i see. so instead of calling `finish()` here, down below when you get `counts.back()`, I would change it to use the internal `detail::read_single_value()` function which takes a queue argument and enqueues the operation. so something like: `return result + detail::read_single_value(counts.get_buffer(), counts.size() - 1, queue);`. This will still block at the end, but will remove the need for two separate host-device synchronization points (which happens now when you explicitly call `finish()`).

---

## Comment 17

> Username: coveralls  
> Created_at: 2014-05-17 15:49:31 UTC  
> Updated_at: 2014-05-20 21:00:52 UTC  
> Url: https://github.com/boostorg/compute/pull/113#issuecomment-43410406  

[![Coverage Status](https://coveralls.io/builds/779969/badge)](https://coveralls.io/builds/779969)  
  
Coverage increased (+0.07%) when pulling **1bb652cbeab8f2fb47264a7975c5071baeb42575 on roshanr95:set_intersection** into **2fffd98c904bcbf6b60a20abe6c08d6fbf7675dd on kylelutz:develop**.

---

## Comment 18

> Username: roshanrags  
> Created_at: 2014-05-17 17:43:42 UTC  
> Url: https://github.com/boostorg/compute/pull/113#discussion_r12769762  

:+1: Neat!

---

## Comment 19

> Username: roshanrags  
> Created_at: 2014-05-17 17:44:32 UTC  
> Url: https://github.com/boostorg/compute/pull/113#issuecomment-43416398  

Done.. :)

---

## Comment 20

> Username: kylelutz  
> Created_at: 2014-05-17 18:39:48 UTC  
> Url: https://github.com/boostorg/compute/pull/113#issuecomment-43419108  

Awesome! Merged!

---
