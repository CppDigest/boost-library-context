# #89 - Add perf_exclusive_scan benchmark [Closed]

> Username: kylelutz  
> Created at: 2014-04-18 03:29:17 UTC  
> Updated at: 2014-05-18 18:44:52 UTC  
> Closed at: 2014-05-18 18:44:52 UTC  
> Url: https://github.com/boostorg/compute/issues/89  

Add a `perf_exclusive_scan` benchmark for the `boost::compute::exclusive_scan()` algorithm. Should be similar to the `perf_partial_sum` benchmark.  
  
Also, ideally, implement comparison benchmark for `thrust::exclusive_scan()`.

---

## Comment 1

> Username: banche  
> Created at: 2014-05-09 01:17:58 UTC  
> Url: https://github.com/boostorg/compute/issues/89#issuecomment-42624891  

I have some spare time this weekend, will try to do it!

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-05-09 03:20:58 UTC  
> Url: https://github.com/boostorg/compute/issues/89#issuecomment-42630012  

Awesome! Thanks!

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-05-18 18:44:52 UTC  
> Url: https://github.com/boostorg/compute/issues/89#issuecomment-43447726  

Implemented in 48687cd29e5422c8c341466bef83059ecd69fae2.
