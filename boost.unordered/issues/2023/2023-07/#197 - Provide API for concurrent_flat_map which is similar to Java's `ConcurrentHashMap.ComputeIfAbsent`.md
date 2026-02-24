# #197 - [Request] Provide API for concurrent_flat_map which is similar to Java's `ConcurrentHashMap.ComputeIfAbsent` [Closed]

> Username: DexinLi  
> Created at: 2023-07-23 09:46:17 UTC  
> Updated at: 2023-08-07 15:40:31 UTC  
> Closed at: 2023-08-07 15:40:31 UTC  
> Url: https://github.com/boostorg/unordered/issues/197  

I couldn't find an API that provides the functionality of the `ComputeIfAbsent` method in Java's ConcurrentHashMap within the concurrent_flat_map library.  
In my case, I want to assign an unique increamental integer id for each different string. The code would be like  
```  
struct Str2ID {  
    concurrent_hash_map<std::string, uint64_t> m;  
    std::atomic<uint64_t> current_id;  
  
    uint64_t getId(const std::string &str) {  
        return m.compute_if_absent(str, [&](const std::string &x){return ++current_id;});  
    }  
};  
```  
In that case, it's different with function like `emplace_or_visit` in concurrent_flat_map since the computation here has side effects.  
An advidsed API would be like  
```  
template<class Comp, class Visit> bool compute_or_visit(const key_type& k, Comp &&comp, Visit &&visit);  
```

---

## Comment 1

> Username: joaquintides  
> Created at: 2023-07-29 08:13:45 UTC  
> Updated at: 2023-07-29 08:15:32 UTC  
> Url: https://github.com/boostorg/unordered/issues/197#issuecomment-1656593034  

Hi, you can get the behavior you're after with the current API and a supporting utility described [here](https://bannalia.blogspot.com/2022/10/deferred-argument-evaluation.html):  
```cpp  
#include <atomic>  
#include <boost/unordered/concurrent_flat_map.hpp>  
#include <cassert>  
#include <cstdint>  
#include <string>  
#include <type_traits>  
  
template<typename F>  
struct deferred_call  
{  
    deferred_call(F f_): f{f_} {}  
  
    using result_type=decltype(std::declval<const F>()());  
  
    operator result_type() const { return f(); }  
  
    template<  
        typename T,  
        std::enable_if_t<std::is_constructible_v<T, result_type>>* =nullptr  
    >  
    constexpr operator T() const { return {f()}; }  
    
    F f;  
};  
  
struct Str2ID {  
    boost::concurrent_flat_map<std::string, uint64_t> m;  
    std::atomic<uint64_t> current_id;  
  
    uint64_t getId(const std::string &str) {  
        uint64_t id;  
        m.try_emplace_or_cvisit(  
            str,  
            deferred_call([&,this] {  
                id = ++current_id;  
                return id;  
            }),  
            [&](const auto& x) {  
                id = x.second;  
            });  
        return id;  
    }  
};  
  
int main()  
{  
    Str2ID x;  
  
    for(uint64_t i = 0; i < 100; ++i) {  
        auto str = std::to_string(i);  
        auto id = x.getId(str);  
        auto id2 = x.getId(str); // call again to check current_id is not incremented now  
        assert(id == i + 1);  
        assert(id == id2);  
    }  
}  
```  
Hope this helps,
