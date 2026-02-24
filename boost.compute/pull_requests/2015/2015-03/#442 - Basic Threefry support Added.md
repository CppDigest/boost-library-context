# #442 Basic Threefry support Added [Merged]

> Username: junmuz  
> Created at: 2015-03-22 13:09:45 UTC  
> Updated at: 2015-06-10 05:52:07 UTC  
> Merged at: 2015-06-10 05:52:07 UTC  
> Closed at: 2015-06-10 05:52:07 UTC  
> Url: https://github.com/boostorg/compute/pull/442  

In these commits, basic Threefry RNG support is added.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-03-24 05:08:18 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27003471  

Can you rename this file to `threefry_engine.hpp` in order to match the class name.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-03-24 05:09:07 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27003482  

I don't think we need to add an extra namespace for this, just `boost::compute::threefry_engine`.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2015-03-24 05:10:33 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27003510  

We could set the default template parameter `T` to `uint_` here (just like in `linear_congruential_engine`).

---

## Comment 4

> Username: kylelutz  
> Created_at: 2015-03-24 05:11:45 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27003538  

I think the copy-constructor also needs to call `load_program()` (or simply copy the program object from `other`).

---

## Comment 5

> Username: kylelutz  
> Created_at: 2015-03-24 05:12:10 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27003547  

Same as with the copy-constructor, we need to copy over the program object also.

---

## Comment 6

> Username: kylelutz  
> Created_at: 2015-03-24 05:13:22 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27003579  

So this kernel takes in pointers to `uint` arrays, will this work if the `threefry_engine` is setup with a type other than `uint_` (e.g. `ushort_` or `ulong_`)?

---

## Comment 7

> Username: kylelutz  
> Created_at: 2015-03-24 05:15:18 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27003616  

Can you add some documentation for this method? What is the difference between `ctr` and `key`. The other random number engines (`mersenne_twister_engine` and `linear_congruential_engine`) take a single output range of type `T` to write the generated values to. Can you explain why this takes two output ranges?

---

## Comment 8

> Username: kylelutz  
> Created_at: 2015-03-24 05:16:42 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27003654  

Hmm, so this kernel only executes with a single work-item? Is there any way to parallelize it to better take advantage of massively-parallel GPU devices?

---

## Comment 9

> Username: kylelutz  
> Created_at: 2015-03-24 05:17:57 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27003695  

I don't think we need this `typedef`. I'd just change `threefry_engine` to have a default template argument of `uint_` and then people could just use `threefry_engine<>` (similar to the API for `linear_congruential_engine<>`).

---

## Comment 10

> Username: kylelutz  
> Created_at: 2015-03-24 05:18:44 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27003712  

Just for consistency, can you name this test `random.threefry_engine` and the source file `test_threefry_engine.cpp`?

---

## Comment 11

> Username: kylelutz  
> Created_at: 2015-03-24 05:20:32 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27003744  

Are these the same numbers that are generated from the `mersenne_twister_engine`? That doesn't seem right to me. Wouldn't the threefry algorithm generate different random numbers?

---

## Comment 12

> Username: kylelutz  
> Created_at: 2015-03-24 05:21:28 UTC  
> Url: https://github.com/boostorg/compute/pull/442#issuecomment-85345175  

Looking good! I've added some review comments. Look them over and let me know what you think. Thanks!

---

## Comment 13

> Username: pavanky  
> Created_at: 2015-03-24 11:36:45 UTC  
> Updated_at: 2015-03-24 11:40:40 UTC  
> Url: https://github.com/boostorg/compute/pull/442#issuecomment-85458618  

~~It is possible for three_fry to generate other data types. You can look at how we do this in ArrayFire: https://github.com/arrayfire/arrayfire/blob/devel/src/backend/opencl/kernel/random.cl~~  
  
On second thought I don't think it is appropriate for the way the generator is implemented in Compute.

---

## Comment 14

> Username: junmuz  
> Created_at: 2015-03-28 13:12:53 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27344971  

Done

---

## Comment 15

> Username: junmuz  
> Created_at: 2015-03-28 13:13:46 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27344975  

I have removed the extra namespace. I was thinking that an added namespacing might be beneficial in grouping the random number functionality.

---

## Comment 16

> Username: junmuz  
> Created_at: 2015-03-28 13:14:07 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27344981  

Done

---

## Comment 17

> Username: junmuz  
> Created_at: 2015-03-28 13:15:00 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27344984  

Yes, I missed it. I have rectified the problem.

---

## Comment 18

> Username: junmuz  
> Created_at: 2015-03-28 13:15:10 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27344985  

Done

---

## Comment 19

> Username: junmuz  
> Created_at: 2015-03-28 13:18:38 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27345001  

There are other threefry types that I'll be adding in future. Overall there are four types  
1. 32x2  
2. 32x4  
3. 64x2  
4. 64x4  
  
I have only added 32x2 up till now, and will be adding the remainder later.

---

## Comment 20

> Username: junmuz  
> Created_at: 2015-03-28 13:22:33 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27345029  

The generated random numbers are contained in the "ctr". "key" is only an input range required for generating RNG.

---

## Comment 21

> Username: junmuz  
> Created_at: 2015-03-28 13:23:29 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27345039  

I have made necessary corrections to it. Can you take a look at it?

---

## Comment 22

> Username: junmuz  
> Created_at: 2015-03-28 13:23:42 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27345040  

I have removed it.

---

## Comment 23

> Username: junmuz  
> Created_at: 2015-03-28 13:24:02 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27345042  

I have corrected it.

---

## Comment 24

> Username: kylelutz  
> Created_at: 2015-03-28 16:37:46 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27346276  

This should be `\class threefry_engine`.

---

## Comment 25

> Username: kylelutz  
> Created_at: 2015-03-28 16:38:17 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27346279  

I think `vector` should actually be `vector_key` here?

---

## Comment 26

> Username: kylelutz  
> Created_at: 2015-03-28 16:42:56 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27346319  

So can you explain the difference between the counter and key values? As a user simply wanting to generate random numbers, do I need to set up the counter values myself? The other random number engines use a generate function which just takes a single output range like this:  
  
```  
template<class OutputIterator>  
void generate(OutputIterator first, OutputIterator last, command_queue &queue)  
```  
  
Is it possible to implement the same interface for `threefry_engine`?

---

## Comment 27

> Username: junmuz  
> Created_at: 2015-03-28 20:12:47 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27348064  

Comment fixed

---

## Comment 28

> Username: junmuz  
> Created_at: 2015-03-28 20:13:20 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27348068  

It was supposed to be vector_ctr. I have fixed it. Tests are passing now.

---

## Comment 29

> Username: junmuz  
> Created_at: 2015-03-28 20:15:11 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27348082  

Yes, it can be done. I think we can keep the counter as an optional parameter. We can use a default value which can be overridden by the user. What should be the default value, I think I have to research a bit over it.

---

## Comment 30

> Username: kylelutz  
> Created_at: 2015-04-01 02:51:41 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27541423  

Yeah, if possible, it would be great to match the same interface used with the other random-number engines. Thanks!

---

## Comment 31

> Username: junmuz  
> Created_at: 2015-04-01 19:38:18 UTC  
> Updated_at: 2015-04-06 04:52:22 UTC  
> Url: https://github.com/boostorg/compute/pull/442#discussion_r27603599  

I have added an overloaded method.

---
