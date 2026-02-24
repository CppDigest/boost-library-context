# #324 - Missing functionality in bulk visit [Open]

> Username: hlverstoep  
> Created at: 2025-10-16 11:04:59 UTC  
> Updated at: 2025-10-16 11:04:59 UTC  
> Url: https://github.com/boostorg/unordered/issues/324  

I want to write a concurrent cache where I can lookup multiple items at once:  
```  
template <class K, class V>  
std::vector<std::optional<V>> lookup(const concurrent_flat_map<K, V>& map, const std::vector<K>& keys){  
    std::vector<std::optional<V>> result;  
    map.cvisit(keys.begin(), keys.end(), [&](const auto& x){  
        // PROBLEM: how to get 'i'??  
        result[i] = x->second;  
    });  
    return result;  
}  
```  
  
I can't find a way to write this with the current API. I would like a new function that traverses an additional iterator in the same order:  
```  
template <class K, class V>  
std::vector<std::optional<V>> lookup(const concurrent_flat_map<K, V>& map, const std::vector<K>& keys){  
    std::vector<std::optional<V>> result;  
    map.cvisit(keys.begin(), keys.end(), result.begin(), [&](const auto& x, auto& it){  
        *it = x->second;  
    });  
    return result;  
}  
```
