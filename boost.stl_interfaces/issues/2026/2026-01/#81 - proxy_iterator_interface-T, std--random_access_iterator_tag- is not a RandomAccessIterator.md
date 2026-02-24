# #81 - proxy_iterator_interface<T, std::random_access_iterator_tag> is not a RandomAccessIterator [Closed]

> Username: sdebionne  
> Created at: 2026-01-20 15:42:28 UTC  
> Updated at: 2026-01-24 23:12:06 UTC  
> Closed at: 2026-01-24 23:12:06 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/81  

The `zip_iterator` from the documentation does not pass the `RandomAccessIterator` concept check from Boost.ConceptCheck. That is:  
  
```c++  
function_requires<boost::RandomAccessIterator<zip_iterator>();   
```  
  
gives:  
  
```  
./boost/concept_check.hpp:209:13: error: conversion from 'std::input_iterator_tag' to non-scalar type 'std::bidirectional_iterator_tag' requested  
```  
  
Maybe that is to be expected because the old `RandomAccessIterator` is not inline with the c++20 concepts one?  
  
Here is the full snippet:  
  
```c++  
#include <boost/concept_check.hpp>  
  
#include <boost/stl_interfaces/iterator_interface.hpp>  
  
#include <algorithm>  
#include <array>  
#include <tuple>  
  
#include <cassert>  
  
  
struct zip_iterator : boost::stl_interfaces::proxy_iterator_interface<  
                      zip_iterator,  
                      std::random_access_iterator_tag,  
                      std::tuple<int, int>,  
                      std::tuple<int &, int &>>  
{  
    constexpr zip_iterator() noexcept : it1_(), it2_() {}  
    constexpr zip_iterator(int * it1, int * it2) noexcept : it1_(it1), it2_(it2)  
    {}  
  
    constexpr std::tuple<int &, int &> operator*() const noexcept  
    {  
        return std::tuple<int &, int &>{*it1_, *it2_};  
    }  
    constexpr zip_iterator & operator += (std::ptrdiff_t i) noexcept  
    {  
        it1_ += i;  
        it2_ += i;  
        return *this;  
    }  
    constexpr auto operator-(zip_iterator other) const noexcept  
    {  
        return it1_ - other.it1_;  
    }  
  
private:  
    int * it1_;  
    int * it2_;  
};  
  
int main()  
{  
    function_requires<boost::RandomAccessIterator<zip_iterator>();   
    return 0;  
}  
```  
  
Context: https://github.com/boostorg/gil/pull/669

---

## Comment 1

> Username: sdebionne  
> Created at: 2026-01-20 16:07:21 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/81#issuecomment-3773714816  

For the records,   
  
```c++  
static_assert(std::random_access_iterator<zip_iterator>);  
```  
  
compiles just fine. So upgrading from Iterator to STLInterfaces might require to upgrade Boost.ConceptCheck to C++20 concepts as well.

---

## Comment 2

> Username: tzlaine  
> Created at: 2026-01-24 23:12:06 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/81#issuecomment-3795682926  

That's right, since `zip_iterator` is a proxy iterator, it cannot be a C++17 `RandomAccessIterator`.  This is called out in the documentation.
