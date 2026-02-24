# #22 - Relax `MutablePriorityQueue` concept to not require that `value_type` be default constructable. [Open]

> Username: kalaxy  
> Created at: 2019-11-01 16:19:47 UTC  
> Updated at: 2019-11-01 16:32:36 UTC  
> Url: https://github.com/boostorg/heap/issues/22  

The `MutablePriorityQueue` concept currently [requires](https://github.com/boostorg/heap/blob/19fda03545e1b85b3a2d3a94b9a3930ec64335d4/include/boost/heap/heap_concepts.hpp#L87) that `value_type` be default constructable.  I don't think that is required by an actual queue implementation.  It would be great if we could relax that requirement.

---

## Comment 1

> Username: kalaxy  
> Created at: 2019-11-01 16:32:36 UTC  
> Url: https://github.com/boostorg/heap/issues/22#issuecomment-548855233  

Here is a small example case.  Everything seems to work fine in the queue, when the element isn't default constructable, but when trying the concept check I get a compiler error.  
```  
#include <boost/heap/skew_heap.hpp>  
  
struct Int {  
    Int( int i ) : _i(i) {};  
    Int& operator=(int i) {  
        _i = i;  
        return *this;  
    }  
    friend bool operator<(Int const &lhs, Int const &rhs) {  
        return lhs._i < rhs._i;  
    }  
    int _i;  
};  
  
int main() {  
    using PQ = boost::heap::skew_heap<Int, boost::heap::mutable_<true>>;  
  
    PQ pq;  
    auto handle = pq.push(1);  
  
    pq.update(handle, 2);  
  
    *handle = 3;  
    pq.update(handle);  
  
    pq.pop();  
  
    // Uncommenting this causes a compile error.  
    // BOOST_CONCEPT_ASSERT((boost::heap::MutablePriorityQueue<PQ>));  
}  
```
