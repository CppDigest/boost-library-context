# #490 CPU-targeted merge sort adapted for sorting by key [Merged]

> Username: jszuppe  
> Created at: 2015-08-03 21:48:00 UTC  
> Updated at: 2015-08-04 07:36:07 UTC  
> Merged at: 2015-08-04 04:46:07 UTC  
> Closed at: 2015-08-04 04:46:07 UTC  
> Url: https://github.com/boostorg/compute/pull/490  

I modified CPU-targeted merge sort (see https://github.com/boostorg/compute/pull/486), so it can also perform sorting by key.  
  
I also restricted usage of `radix_sort` in `sort_by_key.hpp` to only these situations when type of the keys is "radix sortable" (like it's done in [`sort.hpp`](https://github.com/boostorg/compute/blob/develop/include/boost/compute/algorithm/sort.hpp)).

---
