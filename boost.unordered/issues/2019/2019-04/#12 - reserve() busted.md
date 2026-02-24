# #12 - reserve() busted [Closed]

> Username: ncm  
> Created at: 2019-04-26 23:38:30 UTC  
> Updated at: 2021-12-15 19:23:32 UTC  
> Closed at: 2021-12-15 19:23:32 UTC  
> Url: https://github.com/boostorg/unordered/issues/12  

I have discovered that if I have an empty hash table and call `reserve(size)`, the hash table, instead of allocating a big enough bucket table to insert `size * load` items, deletes whatever table it has, and then waits until I am in my critical path to allocate the bucket table.  
  
This bug was installed in `fe3d612f` on 2009-10-04 10:37:36.  
  
In order to work around this bug, I have to insert a dummy element, call `reserve(size)`, and then erase the dummy element.  
  
This behavior violates the entire purpose of `reserve()`, which is to front-load allocations so that they will not stall time-critical operations.  
  
Gnu libstdc++ and Clang libc++ `std::unordered_set` both implement `reserve(size)` correctly.  
  
```  
#include <boost/unordered_set.hpp>  
#include <new>  
#include <functional>  
#include <iostream>  
#include <cstdlib>  
  
int total_allocation = 0;  
  
struct B {  
  B() : i(++count) {}  
  static int count;  
  int i;  
  bool operator==(B const& b) { return i == b.i; };  
  bool operator!=(B const& b) { return i != b.i; };  
};  
  
int B::count = 0;  
  
template<typename T> struct A : B {  
  using value_type = T;  
  
  A() = default;  
  template <class U> A(const A<U>&) noexcept {}  
  
  T* allocate(std::size_t n) {  
    std::cout << "  " << i <<  " allocated " << n << " x " << sizeof(T) << '\n';  
    total_allocation += n * sizeof(T);  
    return (T*) std::calloc(n, sizeof(T));  
  }  
  
  void deallocate(T* p, std::size_t n) noexcept {  
    std::cout << "  " << i <<  " deallocated " << n << " x " << sizeof(T) << '\n';  
    total_allocation -= n * sizeof(T);  
    std::free(p);  
  }  
};  
  
int main() {  
  boost::unordered_set<int, std::hash<int>, std::equal_to<int>, A<int>> s(1000'000);  
  std::cout << "Bucket table allocation = " << total_allocation  
    << ", expected at least " << sizeof(int*) * 1000'000 << '\n';  
  
  s.insert(0);  
  s.reserve(2000'000);  
  s.erase(0);  
  std::cout << "Bucket table allocation = " << total_allocation  
    << ", expect at least " << sizeof(int*) * 2000'000 << '\n';  
  
  s.reserve(3000'000);  
  std::cout << "Bucket table allocation = " << total_allocation  
    << ", expected at least " << sizeof(int*) * 3000'000 << '\n';  
  
  return total_allocation == 0;  
}  
```  
Output is  
```  
Bucket table allocation = 0, expected at least 8000000  
  2 allocated 1 x 24  
  3 allocated 1572870 x 8  
  3 allocated 3145740 x 8  
  3 deallocated 1572870 x 8  
  2 deallocated 1 x 24  
Bucket table allocation = 25165920, expect at least 16000000  
  3 deallocated 3145740 x 8  
Bucket table allocation = 0, expected at least 24000000  
```
