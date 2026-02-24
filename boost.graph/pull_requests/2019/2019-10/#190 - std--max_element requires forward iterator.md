# #190 std::max_element requires forward iterator [Merged]

> Username: bkpoon  
> Created at: 2019-10-16 22:08:03 UTC  
> Updated at: 2021-04-21 18:23:50 UTC  
> Merged at: 2021-04-21 18:23:50 UTC  
> Closed at: 2021-04-21 18:23:50 UTC  
> Url: https://github.com/boostorg/graph/pull/190  

1. Replace `std::max_element` with `boost::first_max_element` in `include/boost/graph/bc_clustering.hpp` as described in #175.  
2. Additionally, `include/boost/graph/howard_cycle_ratio.hpp` has the same issue and is patched similarly in b9c3b14d3918e2beb25d2b8b4b735fc9c142cf7d.  
  
I ran the basic tests. Everything compiles with Xcode 11.1 running on macOS 10.14.6 and it looks like nothing failed, but I'm not sure. I did have to remove `disjoint_sets.hpp` from `include/boost/pending` and `include/boost/pending/detail` for `b2` to work with the `develop` branch.  
  
The testing is how I found the problem with `include/boost/graph/howard_cycle_ratio.hpp`. There is also a `std::max_element` in `include/boost/graph/planar_detail/bucket_sort.hpp`, but it looks like the arguments are acceptable iterators.  
```  
git clone https://github.com/boostorg/boost  
cd boost  
git submodule update --init  
  
<change files in lib/graph>  
  
./bootstrap.sh            <- compile b2  
./b2 headers              <- just installs headers  
./b2                      <- build compiled components  
  
../../../b2               <- run all tests in lib/graph/test  
```  
Let me know if anything else is needed. Thanks!

---

## Comment 1

> Username: bkpoon  
> Created_at: 2019-10-16 22:34:48 UTC  
> Url: https://github.com/boostorg/graph/pull/190#issuecomment-542919118  

I changed the base from `master` to `develop`.

---
