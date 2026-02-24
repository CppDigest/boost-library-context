# #101 - ublas::unbounded_array doesn't work with C++11 allocators [Closed]

> Username: jwakely  
> Created at: 2020-08-05 14:09:06 UTC  
> Updated at: 2020-08-12 06:12:52 UTC  
> Closed at: 2020-08-12 06:12:52 UTC  
> Url: https://github.com/boostorg/ublas/issues/101  

This produces a screenful of errors because you're not using `std::allocator_traits` to access members of the allocator, as required since C++11:  
  
```cpp  
#include <boost/numeric/ublas/matrix.hpp>  
#include <boost/numeric/ublas/io.hpp>  
#include <memory>  
  
template <class Tp>  
  struct SimpleAllocator  
  {  
    typedef Tp value_type;  
  
    SimpleAllocator() noexcept { }  
  
    template <class T>  
      SimpleAllocator(const SimpleAllocator<T>&) { }  
  
    Tp *allocate(std::size_t n)  
    { return std::allocator<Tp>().allocate(n); }  
  
    void deallocate(Tp *p, std::size_t n)  
    { std::allocator<Tp>().deallocate(p, n); }  
  };  
  
template <class T, class U>  
  bool operator==(const SimpleAllocator<T>&, const SimpleAllocator<U>&)  
  { return true; }  
#if __cpp_impl_three_way_comparison < 201907L  
template <class T, class U>  
  bool operator!=(const SimpleAllocator<T>&, const SimpleAllocator<U>&)  
  { return false; }  
#endif  
  
int main ()  
{  
  using namespace boost::numeric::ublas;  
  matrix<int, row_major, unbounded_array<int, SimpleAllocator<int>>> m (3, 3);  
  for (unsigned i = 0; i < m.size1 (); ++ i)  
  for (unsigned j = 0; j < m.size2 (); ++ j)  
    m (i, j) = 3 * i + j;  
  std::cout << m << std::endl;  
}  
```  
  
See also #96 where the problem occurs even for `std::allocator`, and my comments on #100.

---

## Comment 1

> Username: glenfe  
> Created at: 2020-08-12 06:12:52 UTC  
> Url: https://github.com/boostorg/ublas/issues/101#issuecomment-672630421  

Resolved by #103. Thanks @jwakely .
