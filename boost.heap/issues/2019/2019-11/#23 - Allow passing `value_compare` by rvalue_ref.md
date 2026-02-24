# #23 - Allow passing `value_compare` by rvalue_ref. [Open]

> Username: kalaxy  
> Created at: 2019-11-01 18:54:37 UTC  
> Updated at: 2019-11-01 19:03:28 UTC  
> Url: https://github.com/boostorg/heap/issues/23  

Currently the code only supports [making a copy](https://github.com/boostorg/heap/blob/19fda03545e1b85b3a2d3a94b9a3930ec64335d4/include/boost/heap/detail/stable_heap.hpp#L164-L168) of the comparison object.  It would be great to support moving comparison objects into place to permit move-only comparison objects and better efficiency of heavy weight comparison objects.

---

## Comment 1

> Username: kalaxy  
> Created at: 2019-11-01 19:03:28 UTC  
> Url: https://github.com/boostorg/heap/issues/23#issuecomment-548912803  

Here is a simple piece of code which demonstrates the desire.  
```  
#include <boost/heap/skew_heap.hpp>  
  
struct MoveOnlyCmp {  
    MoveOnlyCmp() = default;  
    MoveOnlyCmp(MoveOnlyCmp &&) = default;  
    MoveOnlyCmp& operator=(MoveOnlyCmp &&) = default;  
  
    // Without these there is a compiler error  
    //MoveOnlyCmp(MoveOnlyCmp const&) = default;  
    //MoveOnlyCmp& operator=(MoveOnlyCmp const&) = default;  
  
    bool operator()( int lhs, int rhs ) const {  
        return lhs < rhs;  
    }  
};  
  
int main() {  
    using PQ = boost::heap::skew_heap<int, boost::heap::compare<MoveOnlyCmp>>;  
  
    MoveOnlyCmp cmp{};  
  
    PQ pq( std::move(cmp) );  
    pq.push(1);  
    pq.pop();  
}  
```
