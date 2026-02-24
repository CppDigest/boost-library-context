# #338 - storage_adaptor<std::vector> missing efficient move assignment from std::vector [Open]

> Username: vakokako  
> Created at: 2021-09-28 11:18:59 UTC  
> Updated at: 2021-09-29 12:46:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/338  

Often times to extend the behaviour of `boost::histogram`, we need unsafe access to storage. And there is one performance issue with working with storage_adaptor.  
My use case can be simplified to:  
```cpp  
auto hist = make_histogram_with(std::vector<std::size_t>(), axis::regular(100, 0, 100));  
auto& storage = unsafe_access::storage(hist);  
storage = std::vector<std::size_t>(1000);  
```  
The `storage = std::vector<std::size_t>(1000);` line would not call the move assignment operator, but rather `vector_impl::operator=(const U& u)`, which copies the values instead of just swapping the data pointer.

---

## Comment 1

> Username: vakokako  
> Created at: 2021-09-28 11:48:35 UTC  
> Url: https://github.com/boostorg/histogram/issues/338#issuecomment-929113885  

you could of course do `storage = storage_adaptor<std::vector<std::size_t>>(std::vector<std::size_t>(1000));`, but I think it would be nice if the adaptor would support the direct assignment.

---

## Comment 2

> Username: HDembinski  
> Created at: 2021-09-29 07:15:47 UTC  
> Updated at: 2021-09-29 09:54:53 UTC  
> Url: https://github.com/boostorg/histogram/issues/338#issuecomment-929904541  

For what exactly do you need unsafe access to the storage? What kind of tasks do you want to do which are not possible with the public API?  
  
Your code as written makes no sense, especially from the performance point of view, but also since since the size of the vector does not match with the histogram. The first line is allocating a vector already. The third line is allocating another vector. Even if we supported move assignment, this would still delete the original vector. Allocations are costly, so you want to avoid that. If you need to reset the storage at some point, you can do `h.reset()`. If you need to assign external values, you can do  
```  
std::fill(h.begin(), h.end(), 0);  
```  
or  
```  
std::copy(other.begin(), other.end(), h.begin(), h.end());  
```  
This copy instruction requires that you know about underflow and overflow bins, if your histogram has them. Safer code is  
```  
auto hr = indexed(h);  
std::copy(other.begin(), other.end(), hr.begin(), hr.end());  
```  
which skips over underflow and overflow bins.

---

## Comment 3

> Username: vakokako  
> Created at: 2021-09-29 10:32:10 UTC  
> Url: https://github.com/boostorg/histogram/issues/338#issuecomment-930053275  

The code from the example is just there to show when the call to the `operator=` happens.  
  
The actual task is expanding the bounds to fit the desired values. Our 1d histogram axis doesn't have `growth` option, since that would make the speed for the general `add` method slower. Instead, we added the `addExpanding` method that behaves the same as `add` in `growth` axis.  
  
We actually implemented it in two ways (in both cases we handle over/underflow bins ourselves):  
1. casting axis to `growth` option and then passing the storage, offset, axis and range directly to `detail::fill_n`, which would expand bounds as if our histogram was growing  
2. finding min/max of range, expanding bounds manually, and then using the `add` method on a non-growing histogram.  
  
The second option turned out to be more performant so that's the one we chose.

---

## Comment 4

> Username: HDembinski  
> Created at: 2021-09-29 12:46:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/338#issuecomment-930141668  

Ok, thanks for the additional detail. Have you tried to use `indexed` to add histograms of different sizes? It should be faster than using `fill_n`. If you use the non-public API, your code may break at any time, since we are not guaranteeing that to be stable.
