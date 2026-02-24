# #233 - [FR] Mutable access to stateful hashers and comparators [Open]

> Username: psiha  
> Created at: 2024-02-15 12:26:55 UTC  
> Updated at: 2024-02-27 18:10:12 UTC  
> Url: https://github.com/boostorg/unordered/issues/233  

(like the implementation table_core class template provides)  
I have an exotic use case where the state of the two subject objects references an external 'master' object which can potentially get moved around in memory/has an unstable reference (say as if stored in a std::vector) yet when I need to use the unordered container in question I know for certain that the 'master' object is 'pinned' - and would like to simply then update the reference stored in the hasher...  
  
Yes - this opens up the can of worms of enabling the user the change the hasher in such a way that it potentially changes the hashes it returns - but this is already possible just not as easily (exactly by storing references in hashers) - so simply demand that hashes and comparators be [[ gnu::const ]] or else UB...?

---

## Comment 1

> Username: cmazakas  
> Created at: 2024-02-15 16:43:50 UTC  
> Url: https://github.com/boostorg/unordered/issues/233#issuecomment-1946549874  

If I understand you correctly, I think you mean something like [this](https://godbolt.org/z/KW98qzzEj):  
```cpp  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <memory>  
#include <vector>  
#include <iostream>  
  
struct master_object {  
    int x_=1337;  
};  
  
struct state {  
    master_object*& pobj_;  
};  
  
template<class T>  
struct unified {  
    std::shared_ptr<state> p_;  
  
    bool operator()(T const& lhs, T const& rhs) const {  
        return lhs == rhs;  
    }  
  
    std::size_t operator()(T const&) const noexcept {  
        std::cout <<  
            p_->pobj_->x_   
            << std::endl;  
        return 0;  
    }  
};  
  
int main() {  
    using map_type =  
        boost::unordered_flat_map<int, int, unified<int>, unified<int>>;  
  
    std::vector<master_object> objects(256);  
    auto pobj = objects.data() + 127;  
    pobj->x_ = 7331;  
  
    auto pstate = std::make_shared<state>(pobj);  
  
    map_type map(0, {pstate}, {pstate});  
  
    map.emplace(1,2);  
  
    objects.resize(1024);  
    pobj = objects.data() + 552;  
    pobj->x_ = 1234;  
  
    map.emplace(2,3);  
  
    return map.empty() ? 255 : 0;  
}  
```  
  
Otherwise, I might need to see some pseudocode to properly understand the request.

---

## Comment 2

> Username: psiha  
> Created at: 2024-02-27 12:48:21 UTC  
> Url: https://github.com/boostorg/unordered/issues/233#issuecomment-1966472349  

> If I understand you correctly, I think you mean something like [this](https://godbolt.org/z/KW98qzzEj):  
  
Sort of but not quite:  
- first of all I realize that I can already implement/'hack' this with references to external objects (as already said in the initial description "_but this is already possible just not as easily (exactly by storing references in hashers)_") but that is exactly what I wish to avoid (for the same reasons as https://github.com/boostorg/unordered/issues/232) hence the request  
- if I followed it correctly, your example actually changes the logical state of the hasher (and hence its output): that wouldn't be the case in my usecase - there the hasher and comparator would still play nice and return same results for same inputs (also hinted at in the initial description: only the 'binarry contents'/address of the state changes not its logical contents)  
  
I currently do something like:  
![image](https://github.com/boostorg/unordered/assets/340735/43ba082b-bd95-4039-aa8c-21b334448513)  
and it works as expected.

---

## Comment 3

> Username: cmazakas  
> Created at: 2024-02-27 18:10:11 UTC  
> Url: https://github.com/boostorg/unordered/issues/233#issuecomment-1967323399  

Can you provide a https://godbolt.org/ link that demonstrates your issue/what you're trying to accomplish?  
  
Here's a dummy template that already has Boost setup: https://godbolt.org/z/WG1hzhY8M
