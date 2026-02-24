# #40 Introduced BOOST_ASSERT_HANDLER_IS_NORETURN to make sure short-circuit work [Merged]

> Username: cielavenir  
> Created at: 2024-11-09 11:05:22 UTC  
> Updated at: 2024-11-09 14:44:52 UTC  
> Merged at: 2024-11-09 13:16:01 UTC  
> Closed at: 2024-11-09 13:16:01 UTC  
> Url: https://github.com/boostorg/assert/pull/40  

## Phenomenon  
  
This program:  
  
```  
#define BOOST_ENABLE_ASSERT_HANDLER  
#include <boost/heap/fibonacci_heap.hpp>  
  
int main(){  
    boost::heap::fibonacci_heap<int> hp;  
    hp.push(1);  
    hp.pop();  
}  
```  
  
warns this on gcc 12+:  
  
```  
In file included from /opt/wandbox/boost-1.81.0-gcc-12.3.0/include/boost/heap/fibonacci_heap.hpp:16,  
                 from prog.cc:2:  
In member function 'T& boost::array<T, N>::operator[](size_type) [with T = boost::heap::detail::marked_heap_node<int>*; long unsigned int N = 64]',  
    inlined from 'void boost::heap::fibonacci_heap<T, A0, A1, A2, A3, A4>::consolidate() [with T = int; A0 = boost::parameter::void_; A1 = boost::parameter::void_; A2 = boost::parameter::void_; A3 = boost::parameter::void_; A4 = boost::parameter::void_]' at /opt/wandbox/boost-1.81.0-gcc-12.3.0/include/boost/heap/fibonacci_heap.hpp:715:20:  
/opt/wandbox/boost-1.81.0-gcc-12.3.0/include/boost/array.hpp:117:68: warning: array subscript 64 is above array bounds of 'boost::heap::detail::marked_heap_node<int>* [64]' [-Warray-bounds]  
  117 |             return BOOST_ASSERT_MSG( i < N, "out of range" ), elems[i];  
      |                                                               ~~~~~^  
/opt/wandbox/boost-1.81.0-gcc-12.3.0/include/boost/array.hpp: In member function 'void boost::heap::fibonacci_heap<T, A0, A1, A2, A3, A4>::consolidate() [with T = int; A0 = boost::parameter::void_; A1 = boost::parameter::void_; A2 = boost::parameter::void_; A3 = boost::parameter::void_; A4 = boost::parameter::void_]':  
/opt/wandbox/boost-1.81.0-gcc-12.3.0/include/boost/array.hpp:62:11: note: while referencing 'boost::array<boost::heap::detail::marked_heap_node<int>*, 64>::elems'  
   62 |         T elems[N];    // fixed-size array of elements of type T  
      |           ^~~~~  
```  
  
## Background  
  
This happens because, though https://github.com/boostorg/array/blob/develop/include/boost/array.hpp mentions `return BOOST_ASSERT_MSG( i < N, "out of range" ), elems[i]`, but it does not short-circuit.  
  
## Description  
  
This pull request adds new flag named BOOST_ENABLE_ASSERT_HANDLER_NORETURN. When it is defined, boost_assertion_failed is declared with `[[noreturn]]`. Then gcc can understand the short-circuit that BOOST_ASSERT_MSG does not return when the assertion fails.  
  
Of course, the user's boost_assertion_failed must throw, which can be breaking, so a new flag must be used and BOOST_ENABLE_ASSERT_HANDLER cannot be used automatically.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-11-09 11:52:47 UTC  
> Url: https://github.com/boostorg/assert/pull/40#issuecomment-2466186519  

It's better to use a separate and independent macro, `BOOST_ASSERT_HANDLER_IS_NORETURN`. Also, `[[noreturn]]` should be `BOOST_NORETURN` for earlier C++ standards.

---

## Comment 2

> Username: cielavenir  
> Created_at: 2024-11-09 14:38:57 UTC  
> Url: https://github.com/boostorg/assert/pull/40#issuecomment-2466242605  

Late, but I tested that BOOST_ASSERT_HANDLER_IS_NORETURN fixes my issue locally  
  
And thank you for merging

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-11-09 14:44:52 UTC  
> Url: https://github.com/boostorg/assert/pull/40#issuecomment-2466244263  

You are welcome; and thank you for the PR.

---
