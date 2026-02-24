# #897 - constant_iterator and counting_iterator dereference attempts to bind a const object to a reference [Open]

> Username: tahonermann  
> Created at: 2026-01-06 23:22:44 UTC  
> Updated at: 2026-01-09 16:58:00 UTC  
> Url: https://github.com/boostorg/compute/issues/897  

The following test case illustrates a compilation failure that occurs when attempting to dereference an instance of `constant_iterator` instantiated for a non-const value type.  
```  
#include <boost/compute/iterator/constant_iterator.hpp>  
  
using namespace boost::compute;  
  
int f(constant_iterator<int> ci) {  
  return *ci;  
}  
```  
  
When compiled by the Intel oneAPI compiler (a fork of Clang), the following error (demonstrated using the oneAPI icx driver on Windows) is produced:  
```  
$ icx /c /I../tmp/boost_1_90_0 /EHsc -std:c++20 t.cpp  
...  
In file included from ...\t.cpp:1:  
...\boost_1_90_0\boost\compute\iterator\constant_iterator.hpp(113,16): error:  
      binding reference of type 'int' to value of type 'const int' drops 'const' qualifier  
  113 |         return m_value;  
      |                ^~~~~~~  
...\boost_1_90_0\boost\iterator\iterator_facade.hpp(500,18): note: in instantiation  
      of member function 'boost::compute::constant_iterator<int>::dereference' requested here  
  500 |         return f.dereference();  
      |                  ^  
...\boost_1_90_0\boost\iterator\iterator_facade.hpp(598,38): note: in instantiation  
      of function template specialization  
      'boost::iterators::iterator_core_access::dereference<boost::compute::constant_iterator<int>>' requested here  
  598 |         return iterator_core_access::dereference(this->derived());  
      |                                      ^  
...\t.cpp(6,10): note: in instantiation of member function  
      'boost::iterators::detail::iterator_facade_base<boost::compute::constant_iterator<int>, int,  
      std::random_access_iterator_tag, int &, long long, false, false>::operator*' requested here  
    6 |   return *ci;  
      |          ^  
1 error generated.  
```  
  
The relevant code from the `constant_iterator.hpp` header file is excerpted below (From https://github.com/boostorg/compute/blob/boost-1.90.0/include/boost/compute/iterator/constant_iterator.hpp):  
```  
 34 template<class T>  
 35 class constant_iterator_base  
 36 {  
 37 public:  
 38     typedef ::boost::iterator_facade<  
 39         ::boost::compute::constant_iterator<T>,  
 40         T,  
 41         ::std::random_access_iterator_tag  
 42     > type;  
 43 };  
 ..  
 59 template<class T>  
 60 class constant_iterator : public detail::constant_iterator_base<T>::type  
 61 {  
 ..  
 63     typedef typename detail::constant_iterator_base<T>::type super_type;  
 64     typedef typename super_type::reference reference;  
 ..  
111     reference dereference() const  
112     {  
113         return m_value;  
114     }  
...  
147     T m_value;  
...  
149 };  
```  
  
Iterator dereference for `constant_iterator` is implemented by the `dereference()` member. The problem is that `reference` is defined as `T&`. When `T` is a non-const type, then `reference` is a reference to a non-const type and the attempt to bind `m_value` in the return statement fails because `dereference()` is a const member function and `m_value` is therefore const-qualified when accessed within its definition.  
  
This issue can probably be fixed in the definition of `constant_iterator_base` by passing an appropriately const-qualified type as the argument for the `Reference` template parameter of `iterator_facade`. For example:  
```  
 34 template<class T>  
 35 class constant_iterator_base  
 36 {  
 37 public:  
 38     typedef ::boost::iterator_facade<  
 39         ::boost::compute::constant_iterator<T>,  
 40         T,  
-41         ::std::random_access_iterator_tag  
+++         ::std::random_access_iterator_tag,  
+++         const T&  
 42     > type;  
 43 };  
```  
  
It seems the [`test_constant_iterator.cpp` test](https://github.com/boostorg/compute/blob/boost-1.90.0/test/test_constant_iterator.cpp) fails to exercise the iterator dereference operation, presumably due to `boost::compute::copy()` using a different operation such as `operator[]`.

---

## Comment 1

> Username: tahonermann  
> Created at: 2026-01-07 16:54:33 UTC  
> Url: https://github.com/boostorg/compute/issues/897#issuecomment-3719810709  

> This issue can probably be fixed in the definition of `constant_iterator_base` by passing an appropriately const-qualified type as the argument for the `Reference` template parameter of `iterator_facade`.  
  
Alternatively, perhaps `dereference()` should return `detail::meta_kernel_literal<T>` as `operator[]` does.

---

## Comment 2

> Username: tahonermann  
> Created at: 2026-01-09 16:57:32 UTC  
> Url: https://github.com/boostorg/compute/issues/897#issuecomment-3729800243  

This same issue occurs with `counting_iterator` as well.  
```  
#include <boost/compute/iterator/counting_iterator.hpp>  
  
using namespace boost::compute;  
  
int f(counting_iterator<int> ci) {  
  return *ci;  
}  
```
