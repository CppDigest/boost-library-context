# #24 - boost::heap::skew_heap improperly handles rvalue_reference when creating a node. [Open]

> Username: kalaxy  
> Created at: 2019-11-01 22:49:35 UTC  
> Updated at: 2022-12-28 10:04:26 UTC  
> Url: https://github.com/boostorg/heap/issues/24  

The `skew_heap_node` constructor taking an rvalue reference [makes a copy](https://github.com/boostorg/heap/blob/19fda03545e1b85b3a2d3a94b9a3930ec64335d4/include/boost/heap/skew_heap.hpp#L92-L93) instead of moving it in.  This prevents the use of move only types with `skew_heap`.  
  
I'm pretty sure that you just need to add `std::move` to the initializer list construction. E.g.  
```  
    skew_heap_node(value_type && v):  
        value(std::move(v))  
```

---

## Comment 1

> Username: kalaxy  
> Created at: 2019-11-01 23:05:37 UTC  
> Url: https://github.com/boostorg/heap/issues/24#issuecomment-548979076  

Here is an example which triggers the compiler error.  
```  
#include <boost/heap/skew_heap.hpp>                                                                                                                                     
                                                                                                                                                                        
struct MoveOnlyInt {                                                                                                                                                    
    MoveOnlyInt(int i) : i(i) {}                                                                                                                                        
    MoveOnlyInt(MoveOnlyInt &&) = default;                                                                                                                              
    MoveOnlyInt& operator=(MoveOnlyInt &&) = default;                                                                                                                   
    friend bool operator<( MoveOnlyInt const &lhs, MoveOnlyInt const &rhs ) {                                                                                           
        return lhs.i < rhs.i;  
    }  
    int i;  
};                                                                                                                                                                      
      
int main() {  
    using PQ = boost::heap::skew_heap<MoveOnlyInt>;                                                                                                                     
    PQ pq;                                                                                                                                                              
    pq.emplace(1);                                                                                                                                                      
}  
```  
Switching `skew_heap` for another like `binomial_heap` allows it to compile.

---

## Comment 2

> Username: timblechmann  
> Created at: 2019-11-02 05:03:40 UTC  
> Url: https://github.com/boostorg/heap/issues/24#issuecomment-549011336  

boost.heap is in maintenance mode. but i'm more than happy to integrate PRs

---

## Comment 3

> Username: TimeLikeAlpha  
> Created at: 2022-12-28 10:04:25 UTC  
> Url: https://github.com/boostorg/heap/issues/24#issuecomment-1366524119  

Hey Tim, curious what this "maintenance mode" is?  Does that mean deprecated?
