# #343 - Sort_by_key fails at 6144 elements or more [Closed]

> Username: shehzan10  
> Created at: 2014-12-15 18:06:50 UTC  
> Updated at: 2017-03-21 09:40:57 UTC  
> Closed at: 2017-03-21 09:40:57 UTC  
> Url: https://github.com/boostorg/compute/issues/343  

It looks like sort_by_key fails at any size greater than or equal to 6144 elements.  
The gist has the code I used to test this: https://gist.github.com/shehzan10/2d34a612aa3ee44a145b (its a modifies sort_vector example).  
  
Here is the relevant output:  
  
```  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
terminate called recursively  
  what():  Out of Resources  
Aborted (core dumped)  
```  
  
Other observations:  
- This only happens if `compute::less<type>()` or `compute::greater<type>()` are specified.  
- This bug does not affect sort with or without the direction specified.  
  
This bug may possibly be related to #275.  
##

---

## Comment 1

> Username: pavanky  
> Created at: 2014-12-15 18:58:57 UTC  
> Url: https://github.com/boostorg/compute/issues/343#issuecomment-67045472  

The problem seems to occur because when `compare` parameter is passed, `serial_insertion_sort_by_key` is called always. When `compare` is not passed, `radix_sort` is called at these sizes. The problem seems to be two fold.  
1. Bug in `serial_insertion_sort_by_key` for size >= 6144.  
2. Bug in `sort_by_key` with `Compare` parameter.

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-12-15 19:18:47 UTC  
> Url: https://github.com/boostorg/compute/issues/343#issuecomment-67048635  

Thanks for reporting this and adding the sample code.  
  
Yeah, the insertion sort implementation currently uses local memory to store and sort the input data. For larger data sizes this will request more than the available local memory size at which point it will throw the `"Out of Resources"` error you are seeing. We could probably update this to just sort the data in global memory (though this is not ideal).  
  
But the main issue is that we currently don't have a high-performance parallel comparison sort. I think @roshanr95 is currently working on a parallel merge-sort implementation which should fill this role. This could then be used to efficiently implement `sort()` and `sort_by_key()` with custom compare functions.  
  
Also, we could update `sort_by_key()` to dispatch to radix sort for `less<T>` and `greater<T>` with radix sortable types (similar to how `sort()` does it) which will allow it to work with larger input sizes.

---

## Comment 3

> Username: pavanky  
> Created at: 2014-12-15 19:21:52 UTC  
> Url: https://github.com/boostorg/compute/issues/343#issuecomment-67049133  

@kylelutz I am implementing on the work around based on `sort()`. I will send in a PR soon.

---

## Comment 4

> Username: pavanky  
> Created at: 2014-12-16 20:42:25 UTC  
> Url: https://github.com/boostorg/compute/issues/343#issuecomment-67228300  

@kylelutz The PR I sent in yesterday works for sorted elements, but the indices are not in "stable" order. Do you want to revert this / add another work around or just wait until the new sort from @roshanr95 is available ?

---

## Comment 5

> Username: kylelutz  
> Created at: 2014-12-17 03:38:08 UTC  
> Url: https://github.com/boostorg/compute/issues/343#issuecomment-67273089  

I think it's fine if `sort_by_key()` is not stable (in the same way `sort()` that is not guaranteed to be stable). If we end up implementing a `stable_sort_by_key()` algorithm then we would have to use a different algorithm (like merge sort).

---

## Comment 6

> Username: roshanrags  
> Created at: 2014-12-24 16:31:49 UTC  
> Url: https://github.com/boostorg/compute/issues/343#issuecomment-68062321  

Sorry for the late reply.. was on vacation..  
  
Merge sort should be done by this weekend...

---

## Comment 7

> Username: jszuppe  
> Created at: 2016-06-27 16:40:13 UTC  
> Url: https://github.com/boostorg/compute/issues/343#issuecomment-228801952  

Fixed, see https://github.com/boostorg/compute/pull/619.
