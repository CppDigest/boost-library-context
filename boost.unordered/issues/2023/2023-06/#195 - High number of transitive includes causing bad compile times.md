# #195 - High number of transitive includes causing bad compile times [Open]

> Username: BurningEnlightenment  
> Created at: 2023-06-26 10:05:07 UTC  
> Updated at: 2023-07-03 17:05:08 UTC  
> Url: https://github.com/boostorg/unordered/issues/195  

I really like the implementation quality of this library. However, consider the following test program:  
```cpp  
#ifdef STD_UNORDERED  
#include <unordered_map>  
#endif  
#ifdef BST_UNORDERED_FWD  
#include <boost/unordered/unordered_flat_map_fwd.hpp>  
#endif  
#ifdef BST_UNORDERED  
#include <boost/unordered/unordered_flat_map.hpp>  
#endif  
  
int main()  
{  
}  
```  
and observe the compile time with different preprocessor configurations (after prewarming the filesystem cache):  
```  
λ  time g++-12 -std=c++20 -O2 -I/home/me/ldevel/vcpkg/installed/x64-linux/include compile-test.cpp -o compile-test  
g++-12 -std=c++20 […] 0.01s user 0.02s system 99% cpu 0.033 total  
λ  time g++-12 -std=c++20 -I/home/me/ldevel/vcpkg/installed/x64-linux/include -O2 -DSTD_UNORDERED compile-test.cpp -o compile-test  
g++-12 -std=c++20 […] 0.15s user 0.02s system 99% cpu 0.170 total  
λ  time g++-12 -std=c++20 -O2 -DBST_UNORDERED_FWD -I/home/me/ldevel/vcpkg/installed/x64-linux/include compile-test.cpp -o compile-test  
g++-12 -std=c++20 […] 0.49s user 0.02s system 99% cpu 0.517 total  
λ  time g++-12 -std=c++20 -O2 -DBST_UNORDERED -I/home/me/ldevel/vcpkg/installed/x64-linux/include compile-test.cpp -o compile-test  
g++-12 -std=c++20 […] 0.75s user 0.03s system 99% cpu 0.782 total  
```  
| flags | time |  
| ------------- | ------------- |  
|  | `0.033s` |  
| `-DSTD_UNORDERED`  | `0.170s`  |  
| `-DBST_UNORDERED_FWD`  | `0.517s`  |  
| `-DBST_UNORDERED`  | `0.782s`  |  
  
System information:  
* g++-12 (Ubuntu 12.1.0-2ubuntu1~22.04) 12.1.0  
* CPU: Intel(R) Core(TM) i9-9980HK  
  
The issue seems to be that `unordered_flat_map` always includes `boost/container_hash/hash.hpp` which in turn includes large parts of the standard library (`<string>`, `<complex> => <sstream>`, `variant`).  
  
It seems somewhat unfortunate to me that the compile time is increased that much, especially since the `unordered_flat_map` is usually part of a composite data structure and therefore transitively included in many TUs.  
  
Furthermore I usually don't use `boost::hash` but a custom hash implementation (SpookyHashv2, xxHash or domain specific hashes) and therefore would like to ask whether it'd be possible to provide a variant which either uses `std::hash` as default `Hash` parameter or doesn't supply one at all.

---

## Comment 1

> Username: cmazakas  
> Created at: 2023-07-03 17:05:07 UTC  
> Url: https://github.com/boostorg/unordered/issues/195#issuecomment-1618896023  

Hello, I'm the maintainer for this repo.  
  
I just wanted to let you know that we've seen your issue.   
  
Currently, I'm on a bout of parental leave so I won't be able to address this issue right now, but we've started discussing it internally.
