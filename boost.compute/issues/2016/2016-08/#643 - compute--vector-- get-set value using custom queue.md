# #643 - compute::vector<> get/set value using custom queue [Closed]

> Username: dPavelDev  
> Created at: 2016-08-16 19:52:16 UTC  
> Updated at: 2016-08-23 20:01:36 UTC  
> Closed at: 2016-08-23 20:01:33 UTC  
> Url: https://github.com/boostorg/compute/issues/643  

When I have to get/set some values from some index from vector using custom queue, I forced to use compute::copy function, not **[]** or **at** members. I can't use convenient methods to access elements!   
  
I think, it is necessary to provide method "set_default_queue(queue)" and public method get_default_queue() for all containers.  
  
Also, is it right to wright code similar to?  
  
``` cpp  
struct my {  
    int x, y;  
}  
  
BOOST_COMPUTE_ADAPT_STRUCT...  
compute::array<my, 42> a;  
a[7].x = 6; // this line (get my struct, edit member, set value)  
```

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-08-16 20:04:37 UTC  
> Updated at: 2016-08-16 20:06:05 UTC  
> Url: https://github.com/boostorg/compute/issues/643#issuecomment-240220930  

IMHO, it's worth considering. However, that would require `buffer_value<T>` class to store `command_queue` object.  
  
If we don't want to store `command_queue` object in `vector` (and other containers), then we can at least provide `at()` method which takes `command_queue` as its 2nd argument. That would also be simpler and introduce less changes to containers.

---

## Comment 2

> Username: dPavelDev  
> Created at: 2016-08-17 09:10:56 UTC  
> Updated at: 2016-08-17 09:11:10 UTC  
> Url: https://github.com/boostorg/compute/issues/643#issuecomment-240356920  

> If we don't want to store command_queue object in vector (and other containers), then we can at least provide at() method which takes command_queue as its 2nd argument.   
  
It's rather good idea for getting value but how to set value? Operator = uses own command queue

---

## Comment 3

> Username: jszuppe  
> Created at: 2016-08-17 09:19:48 UTC  
> Url: https://github.com/boostorg/compute/issues/643#issuecomment-240358859  

It's easy. Method `at()` does not return reference to the value itself but rather a `buffer_value<T>` object that wraps that value. If we modify `buffer_value<T>` to store `command_queue` object then [here](https://github.com/boostorg/compute/blob/master/include/boost/compute/detail/buffer_value.hpp#L121) we can use that queue instead of making a new one.

---

## Comment 4

> Username: jszuppe  
> Created at: 2016-08-17 13:55:36 UTC  
> Url: https://github.com/boostorg/compute/issues/643#issuecomment-240418541  

And just because I'm curious: Can you tell me where you use such a small array in GPU computing? I mean, in most cases `operator[]` is not even needed since in GPGPU you operate on rather large sets of data, not single values.

---

## Comment 5

> Username: dPavelDev  
> Created at: 2016-08-17 19:12:53 UTC  
> Url: https://github.com/boostorg/compute/issues/643#issuecomment-240516361  

Yes, you're right, operator[] needs rarely. For example, I have change only one value. When I build BVH-tree on GPU using my own algorithm, tree root value (only tree root) has undefined value. There are two ways: write kernel to modify only the one value or change this from host.

---

## Comment 6

> Username: jszuppe  
> Created at: 2016-08-18 10:35:00 UTC  
> Url: https://github.com/boostorg/compute/issues/643#issuecomment-240686495  

OK, thanks. I've just remembered, you can always do this:  
  
``` cpp  
compute::array<int_> a(context);  
(a.begin() + 7).write(6, queue)  
```

---

## Comment 7

> Username: dPavelDev  
> Created at: 2016-08-18 11:40:21 UTC  
> Url: https://github.com/boostorg/compute/issues/643#issuecomment-240698860  

Read and write members with custom queue in buffer_iterator. Excellent! But nothing about it in documentation.
