# #232 - [FR] Unified hash and equality predicate (stateful) objects [Open]

> Username: psiha  
> Created at: 2024-02-15 12:11:31 UTC  
> Updated at: 2024-02-27 12:28:15 UTC  
> Url: https://github.com/boostorg/unordered/issues/232  

Consider a usecase with stateful comparators and hashers where they both share the same state (IOW they are the same object providing two functions - one for hashing and one for comparison) - would you consider adding this possibility?  
For example by adding an empty 'flag' comparator type SameAsHasher (or something to that effect)?  
  
(to avoid the duplication or 'smaller' duplication where the hasher and comparator store a reference to an external object as well as the associated more complex codegen)

---

## Comment 1

> Username: cmazakas  
> Created at: 2024-02-15 15:31:35 UTC  
> Url: https://github.com/boostorg/unordered/issues/232#issuecomment-1946331896  

Hmm, I think if I understand correctly, you just want something like [this](https://godbolt.org/z/YfKTes58e):  
```cpp  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <memory>  
  
template<class T>  
struct unified {  
    struct state;  
    std::shared_ptr<state> p_;  
  
    bool operator()(T const& lhs, T const& rhs) const {  
        return lhs == rhs;  
    }  
  
    std::size_t operator()(T const&) const noexcept {  
        return 0;  
    }  
};  
  
int main() {  
    using map_type =  
        boost::unordered_flat_map<int, int, unified<int>, unified<int>>;  
  
    map_type map;  
    map.emplace(1,2);  
    return map.empty() ? 255 : 0;  
}  
```  
  
This should already be supported the library. Otherwise, I might need some pseudocode to fully understand the request.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-02-15 16:58:47 UTC  
> Url: https://github.com/boostorg/unordered/issues/232#issuecomment-1946585420  

Presumably the idea is to avoid the dynamic allocation of `state`, although since the map already allocates, it's not clear how much this would save in practice.

---

## Comment 3

> Username: psiha  
> Created at: 2024-02-27 12:28:14 UTC  
> Url: https://github.com/boostorg/unordered/issues/232#issuecomment-1966438227  

It isn't about avoiding allocation or shared_ptr bloat as it isn't necessary. As mentioned above it is about avoiding the:  
- boilerplate complexity ("duplication where the hasher and comparator store a reference to an external object") as is evident from cmazakas' code  
- codegen complexity (overhead/inefficiency) - an extra layer of pointer indirection (with possible locality of reference issues) and that through two pointers (to the same thing - more pressure on the compiler for alias analysis, register allocation...)
