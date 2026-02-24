# #81 - boost multi_index_container with custom allocator containing array with size depending on the template parameter - compilation failed [Closed]

> Username: itwasntme-mk  
> Created at: 2025-03-17 15:59:50 UTC  
> Updated at: 2025-07-15 16:07:28 UTC  
> Closed at: 2025-07-15 16:07:28 UTC  
> Url: https://github.com/boostorg/multi_index/issues/81  

Smallest code:  
  
```cpp  
#include <boost/multi_index_container.hpp>  
#include <boost/multi_index/random_access_index.hpp>  
  
using namespace boost::multi_index;  
  
template <typename T>  
struct pool_t  
  {  
  typedef T value_type;  
  typedef T* pointer;  
  typedef const T* const_pointer;  
  typedef T& reference;  
  typedef const T& const_reference;  
  typedef std::size_t size_type;  
  typedef std::ptrdiff_t difference_type;  
  typedef std::true_type propagate_on_container_move_assignment;  
  
  template <typename U>  
  struct rebind  
  {  
    typedef pool_t<U> other;  
  };  
  
  typedef std::true_type is_always_equal;  
  
  pool_t() {}  
  
  template <typename T2>  
  pool_t(const pool_t<T2>& ) {}  
  
  T* allocate(size_type n) { return nullptr; }  
  void deallocate(T* ptr, size_type n) {}  
  
  char object[sizeof(T)];  
};  
  
using block_index_t = multi_index_container<  
  int,  
  indexed_by<  
    random_access<>  
  >,  
  pool_t<int>  
>;  
  
block_index_t block_index;  
  
  
int main()  
{  
    return 0;  
}  
```  
  
Compilation failed (gcc 14.2, clang 20.1.0) in line `char object[sizeof(T)];`  
  
Boost version 1.87.0

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-04-14 11:14:38 UTC  
> Url: https://github.com/boostorg/multi_index/issues/81#issuecomment-2801363204  

Yes, as you have found out Boost.MultiIndex requires that the allocator work with incomplete types because of this:  
  
https://github.com/boostorg/multi_index/blob/a52810fc3d246384ca8a74d0f9676bc58e216735/include/boost/multi_index/detail/rnd_index_node.hpp#L30-L44  
  
Basically, `node_allocator`  is defined as `allocator<random_access_index_node_impl>`,  where `random_access_index_node_impl` is the structure being defined, and so incomplete at the point of `node_allocator` instantiation.  
  
I don't see an easy solution from Boost.MultiIndex's side. FWIW, your allocator as defined is not compliant: a copy `al2` of an allocator `al` is not equivalent to `al`, and the standard mandates that this be the case. Here's a conforming example using Howard Hinnant's [`short_alloc`](https://howardhinnant.github.io/stack_alloc.html):  
  
https://godbolt.org/z/376YTzxca  
```cpp  
#include <boost/multi_index_container.hpp>  
#include <boost/multi_index/random_access_index.hpp>  
#include <https://howardhinnant.github.io/short_alloc.h>  
#include <iostream>  
  
using namespace boost::multi_index;  
  
static constexpr std::size_t block_index_arena_size = 10000;  
  
using block_index_t = multi_index_container<  
    int,  
    indexed_by<  
      random_access<>  
    >,  
    short_alloc<int, block_index_arena_size>  
>;  
  
block_index_t::allocator_type::arena_type ar;  
  
block_index_t block_index{  
    block_index_t::ctor_args_list{},  
    block_index_t::allocator_type{ar}};  
  
int main()  
{  
    block_index.push_back(0);  
    block_index.push_back(1);  
    block_index.push_back(2);  
  
    for(const auto& x: block_index) {  
        std::cout << x << " ";  
    }  
  
    return 0;  
}  
  
```

---

## Comment 2

> Username: joaquintides  
> Created at: 2025-07-15 16:07:28 UTC  
> Url: https://github.com/boostorg/multi_index/issues/81#issuecomment-3074278359  

Closing due to lack of feedback from OP.
