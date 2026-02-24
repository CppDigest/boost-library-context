# #197 - small_vector::swap causes spurious allocations and suboptimal performance [Closed]

> Username: sehe  
> Created at: 2021-10-08 18:06:49 UTC  
> Updated at: 2021-10-31 23:23:01 UTC  
> Closed at: 2021-10-31 23:23:01 UTC  
> Url: https://github.com/boostorg/container/issues/197  

Inspired by [this StackOverflow question](https://stackoverflow.com/q/69458701/85371).  
  
Applies to all recent versions tested.  
  
We observed that swapping a small_vector with an empty instance allocates potentially a lot of unused memory:  
  
```c++  
#include <boost/container/small_vector.hpp>  
  
int main() {  
    boost::container::small_vector<int, 4> a, b;  
    assert(b.capacity() == 4);  
  
    a.resize(10'000);  
    a.swap(b);  
    assert(b.capacity() == 10'000);  
    assert(a.capacity() != 10'000); // FAILS  
}  
```  
  
It can be further observed that `std::swap(a, b)` instead works as expected.  
  
Looking into member swap, indicates that even for the simple default stateless allocators the evaluation of propagation rules prevent a simple resource swap: the wrong branch is taken:  
https://github.com/boostorg/container/blob/760026726a4e1bdd44ae4da26f634431b20e6e6e/include/boost/container/vector.hpp#L2554  
  
I suspect that a more optimal evaluation should be correct, perhaps a trait for small_vector_allocator is not tested?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-10-27 07:10:46 UTC  
> Url: https://github.com/boostorg/container/issues/197#issuecomment-952606211  

Thanks for the report. Reviewing other implementations I think it has the same behaviour:  
  
https://github.com/llvm/llvm-project/blob/release/13.x/llvm/include/llvm/ADT/SmallVector.h#L946  
  
resource swap is (in general) only possible if both containers are pointing to heap memory. In this case, one is pointing to heap and the empty one is pointing to internal storage. So we can't just swap pointers.  
  
The difference with std:.swap is that a move constructor is used and the object to be constructed has no storage yet, so it can adopt the heap storage of the moved object. In this swap implementation, if storage is not swappable, I think it's reasonable to check if one of the objects is empty and the other has transferable storage, and perform a partial transfer. It complicates the code, though, I'll need to check the corner cases.

---

## Comment 2

> Username: sehe  
> Created at: 2021-10-28 23:30:28 UTC  
> Url: https://github.com/boostorg/container/issues/197#issuecomment-954297727  

That actually makes a lot of sense. Thanks for clarifying!
