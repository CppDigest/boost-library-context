# #12 - Undefined behaviour in ~container_interface [Closed]

> Username: AndWass  
> Created at: 2019-12-20 10:53:13 UTC  
> Updated at: 2020-01-01 19:32:39 UTC  
> Closed at: 2020-01-01 19:32:39 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/12  

The call to `v1_dtl::clear_impl<Derived>::call(derived());` in `~container_interface()` can be UB since it can call a method on the derived class which has already been destroyed.  
  
Example program that will not work correctly in Debug mode on MSVC:  
  
```cpp  
#include <boost/stl_interfaces/container_interface.hpp>  
#include <vector>  
  
using namespace boost::stl_interfaces;  
  
struct test: container_interface<test, contiguous>  
{  
    using value_type = int;  
    using pointer = int *;  
    using const_pointer = int const *;  
    using reference = value_type &;  
    using const_reference = value_type const &;  
    using size_type = std::size_t;  
    using difference_type = std::ptrdiff_t;  
    using iterator = typename std::vector<int>::iterator;  
    using const_iterator = typename std::vector<int>::const_iterator;  
    using reverse_iterator = boost::stl_interfaces::reverse_iterator<iterator>;  
    using const_reverse_iterator =  
        boost::stl_interfaces::reverse_iterator<const_iterator>;  
  
    iterator erase(const_iterator begin, const_iterator end) {  
        return v.erase(begin, end);  
    }  
  
    iterator begin() {  
        return v.begin();  
    }  
  
    iterator end() {  
        return v.end();  
    }  
  
    std::vector<int> v;  
};  
int main() {  
    {  
        test t;  
        // Destruction of t will cause MSVC debug iterators to fault.  
    }  
}  
```
