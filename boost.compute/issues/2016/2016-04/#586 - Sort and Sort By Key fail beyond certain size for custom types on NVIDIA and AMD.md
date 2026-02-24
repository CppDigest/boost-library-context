# #586 - Sort and Sort By Key fail beyond certain size for custom types on NVIDIA and AMD [Closed]

> Username: shehzan10  
> Created at: 2016-04-21 18:59:57 UTC  
> Updated at: 2017-03-21 09:40:42 UTC  
> Closed at: 2017-03-21 09:40:42 UTC  
> Url: https://github.com/boostorg/compute/issues/586  

I was working with std::pair sorting. The kernel fails with `Out of Resources` error.  
This only happens on NVIDIA and AMD. Intel works fine.  
  
A standalone code to test the bug is attached (rename to .cpp).  
Compilation command:  
  
```  
g++ -std=c++11 sort_vector.cpp -o sort_vector -I /path/to/compute/include/ -lOpenCL -lm  
```  
  
Gist: https://gist.github.com/shehzan10/938d22d2ed68baf4b48ad85f66a612f1  
Attachment: [sort_vector.txt](https://github.com/boostorg/compute/files/230548/sort_vector.txt)  
##

---

## Comment 1

> Username: pavanky  
> Created at: 2016-04-21 19:22:50 UTC  
> Url: https://github.com/boostorg/compute/issues/586#issuecomment-213078511  

Problem location:  
 https://github.com/boostorg/compute/blob/master/include/boost/compute/algorithm/detail/insertion_sort.hpp#L137

---

## Comment 2

> Username: jszuppe  
> Created at: 2016-04-21 19:29:27 UTC  
> Url: https://github.com/boostorg/compute/issues/586#issuecomment-213080475  

This is related to https://github.com/boostorg/compute/issues/343. We do not have comparison sort implementation yet. I'll add before July.

---

## Comment 3

> Username: pavanky  
> Created at: 2016-04-21 19:31:43 UTC  
> Url: https://github.com/boostorg/compute/issues/586#issuecomment-213081612  

Welp, forgot that we reported the other issue as well.

---

## Comment 4

> Username: korhaliv  
> Created at: 2016-05-05 11:49:53 UTC  
> Url: https://github.com/boostorg/compute/issues/586#issuecomment-217134886  

I get the same error when trying to sort vector of any custom type (and obviously custom compare func) with length >  4096 elements. Is there currently any workaround for this?

---

## Comment 5

> Username: jszuppe  
> Created at: 2016-05-05 12:03:23 UTC  
> Updated at: 2016-05-05 12:05:22 UTC  
> Url: https://github.com/boostorg/compute/issues/586#issuecomment-217136726  

@dot3bumpmap Currently, there is not. I'm working on a comparison sort for GPUs. We also need `radix_sort` to be rewritten using `meta_kernel` class (so you can use custom type as a value type in `sort_by_key` algorithm).

---

## Comment 6

> Username: jszuppe  
> Created at: 2016-06-27 16:40:17 UTC  
> Url: https://github.com/boostorg/compute/issues/586#issuecomment-228801972  

Fixed, see https://github.com/boostorg/compute/pull/619.

---

## Comment 7

> Username: korhaliv  
> Created at: 2016-06-28 17:19:37 UTC  
> Url: https://github.com/boostorg/compute/issues/586#issuecomment-229118449  

Awesome work! Solves an issue for me.
