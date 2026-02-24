# #231 - perf_merge fails for odd sized inputs [Closed]

> Username: kylelutz  
> Created at: 2014-08-12 04:25:27 UTC  
> Updated at: 2014-08-15 02:39:34 UTC  
> Closed at: 2014-08-15 02:39:34 UTC  
> Url: https://github.com/boostorg/compute/issues/231  

Running the `perf_merge` benchmark fails for odd-sized inputs (e.g. `perf_merge 9` results in `"ERROR: merged ranges different"`). This seems to be due to creating each input vector with `PERF_N / 2` values which will truncate and leave one item off the end.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-08-14 03:55:13 UTC  
> Url: https://github.com/boostorg/compute/issues/231#issuecomment-52141277  

Fixed in PR #235
