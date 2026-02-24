# #122 - Any interest to `boost::find_gap` function? [Open]

> Username: denzor200  
> Created at: 2024-12-07 00:09:11 UTC  
> Updated at: 2024-12-09 17:44:31 UTC  
> Url: https://github.com/boostorg/algorithm/issues/122  

```  
std::vector<int> nums = {6,7,8,10,11}; // lost 9  
boost::find_gap(nums, 6); // will point to value 10 at index 3  
```  
This function is useful for validating sorted data that are expected to be consecutive integers, for example - packet ids that might be lost, unix timestamps in a video archive that might be sparsed, etc..

---

## Comment 1

> Username: denzor200  
> Created at: 2024-12-07 00:10:08 UTC  
> Url: https://github.com/boostorg/algorithm/issues/122#issuecomment-2524680446  

O(n) in time and constant space  
```  
template<typename It>  
It find_gap(It it, It end, int i) {  
    for (; it!=end; ++it) {  
        if (*it != i++) {  
            return it;  
        }  
    }  
    return end;  
}  
```

---

## Comment 2

> Username: jgopel  
> Created at: 2024-12-09 16:08:49 UTC  
> Updated at: 2024-12-09 16:10:40 UTC  
> Url: https://github.com/boostorg/algorithm/issues/122#issuecomment-2528520181  

A decision here would be up to @mclow I belive - I just have a few comments out of curiosity. What is the purpose of the parameter `i`? Just a start value? Why not infer that from the start of the range or maybe have it be optionally inferred?  
  
Also, I think this would be good to implement in terms of other algorithms. Fundamentally this is a 2-wide sliding window problem. When structured as-such, it pretty elegantly fits itself into `std::adjacent_find` (or the `boost` equivalent). Impl here: https://godbolt.org/z/4PxMT58W1

---

## Comment 3

> Username: mclow  
> Created at: 2024-12-09 17:44:29 UTC  
> Url: https://github.com/boostorg/algorithm/issues/122#issuecomment-2528875072  

I concur with @jgopel here; this is basically `find_adjacent` with a custom predicate.  
Doesn't means that it couldn't be useful, though.
