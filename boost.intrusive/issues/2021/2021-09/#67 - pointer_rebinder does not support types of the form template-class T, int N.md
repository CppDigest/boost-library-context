# #67 - pointer_rebinder does not support types of the form template<class T, int N> [Closed]

> Username: jcelerier  
> Created at: 2021-09-05 11:54:46 UTC  
> Updated at: 2021-11-18 11:15:12 UTC  
> Closed at: 2021-11-18 11:15:11 UTC  
> Url: https://github.com/boostorg/intrusive/issues/67  

This leads e.g.   
  
```C++  
#include <boost/container/small_vector.hpp>  
  
template<typename T, std::size_t X = 32>  
struct alloc  
{   
  using value_type = T;  
  void* allocate(std::size_t n);  
  void deallocate(void* p, std::size_t n);  
};  
  
boost::container::small_vector<int, 16, alloc<int>> vec;  
```  
  
to fail to build (just removing `std::size_t X = 32` makes it work).  
  
This is because   
  
https://github.com/boostorg/intrusive/blob/develop/include/boost/intrusive/pointer_rebind.hpp#L99  
  
does not work - and sadly there's no absolutely generic way in C++ to match all type & non type template arguments.  
  
Would it be possible to at least add an overload for things with a single non-type parameter ?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-11-18 11:15:11 UTC  
> Url: https://github.com/boostorg/intrusive/issues/67#issuecomment-972770505  

The standard way of providing a rebinding mechanism for allocators since C++03 is to define an internal struct and typedef:  
  
```C++  
#include <boost/container/small_vector.hpp>  
  
template<typename T, std::size_t X = 32>  
struct alloc  
{   
   template <class U>  
   struct rebind  
   {  
      typedef alloc<U, X> other;  
   };  
  
  using value_type = T;  
  void* allocate(std::size_t n);  
  void deallocate(void* p, std::size_t n);  
};  
```  
That should be correctly detected by Boost.Container's machinery.
