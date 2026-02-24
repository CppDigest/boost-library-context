# #44 - -DBOOST_ENABLE_ASSERT_HANDLER warns -Warray-bounds [Closed]

> Username: cielavenir  
> Created at: 2024-11-08 05:42:24 UTC  
> Updated at: 2024-11-09 14:49:58 UTC  
> Closed at: 2024-11-08 08:53:19 UTC  
> Url: https://github.com/boostorg/heap/issues/44  

This code:  
  
```  
#define BOOST_ENABLE_ASSERT_HANDLER  
#include <boost/heap/fibonacci_heap.hpp>  
  
int main(){  
    boost::heap::fibonacci_heap<int> hp;  
    hp.push(1);  
    hp.pop();  
}  
```  
  
Raises this warning on gcc 12/13:  
  
```  
In file included from /opt/wandbox/boost-1.81.0-gcc-12.3.0/include/boost/heap/fibonacci_heap.hpp:16,  
                 from prog.cc:1:  
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
  
https://wandbox.org/permlink/3NjP3T7CpAsmpzEm  
  
----  
  
Sorry, I'm not sure if I should file the issue on heap/array/assert

---

## Comment 1

> Username: cielavenir  
> Created at: 2024-11-08 08:34:17 UTC  
> Url: https://github.com/boostorg/heap/issues/44#issuecomment-2464123068  

Adding this fixes the issue https://wandbox.org/permlink/l8XhFqiWmYhr7d0O  
  
```  
namespace boost  
{  
    __attribute__ ((__noreturn__)) void assertion_failed(char const * expr, char const * function, char const * file, long line); // user defined  
    __attribute__ ((__noreturn__)) void assertion_failed_msg(char const * expr, char const * msg, char const * function, char const * file, long line); // user defined  
}  
```

---

## Comment 2

> Username: timblechmann  
> Created at: 2024-11-08 08:53:19 UTC  
> Url: https://github.com/boostorg/heap/issues/44#issuecomment-2464158013  

aehm, BOOST_ENABLE_ASSERT_HANDLER requires users to define the assertion_failed messages: https://www.boost.org/doc/libs/1_86_0/libs/assert/doc/html/assert.html   
  
feel free to reopen, if it actually causes a assertion failure (runtime error) when running boost.heap

---

## Comment 3

> Username: cielavenir  
> Created at: 2024-11-09 02:28:31 UTC  
> Url: https://github.com/boostorg/heap/issues/44#issuecomment-2465998022  

@timblechmann i don't have permission to reopen

---

## Comment 4

> Username: cielavenir  
> Created at: 2024-11-09 02:34:35 UTC  
> Url: https://github.com/boostorg/heap/issues/44#issuecomment-2466000104  

Now I change my question. I have a project with two files  
  
prog.cc  
  
```  
#define BOOST_ENABLE_ASSERT_HANDLER  
  
namespace boost  
{  
    // some magic to shut up gcc 12+ : do need to ask why it is required  
    // __attribute__ ((__noreturn__)) void assertion_failed(char const * expr, char const * function, char const * file, long line); // user defined  
    // __attribute__ ((__noreturn__)) void assertion_failed_msg(char const * expr, char const * msg, char const * function, char const * file, long line); // user defined  
}  
  
#include <boost/heap/fibonacci_heap.hpp>  
  
int main(){  
    boost::heap::fibonacci_heap<int> hp;  
    hp.push(1);  
    hp.pop();  
}  
```  
  
assertion_failed.cc  
  
```  
#include <stdexcept>  
namespace boost  
{  
    void assertion_failed(char const * expr, char const * function, char const * file, long line){  
        throw std::runtime_error(expr);  
        (void)function;  
        (void)file;  
        (void)line;  
    }  
    void assertion_failed_msg(char const * expr, char const * msg, char const * function, char const * file, long line){  
        throw std::runtime_error(expr);  
        (void)msg;  
        (void)function;  
        (void)file;  
        (void)line;  
    }  
}  
```  
  
https://wandbox.org/permlink/1UtnWOMXIVSL3I62  
  
This runs fine, but it raises warning:  
  
```  
In file included from /opt/wandbox/boost-1.81.0-gcc-12.3.0/include/boost/heap/fibonacci_heap.hpp:16,  
                 from prog.cc:10:  
In member function 'T& boost::array<T, N>::operator[](size_type) [with T = boost::heap::detail::marked_heap_node<int>*; long unsigned int N = 64]',  
    inlined from 'void boost::heap::fibonacci_heap<T, A0, A1, A2, A3, A4>::consolidate() [with T = int; A0 = boost::parameter::void_; A1 = boost::parameter::void_; A2 = boost::parameter::void_; A3 = boost::parameter::void_; A4 = boost::parameter::void_]' at /opt/wandbox/boost-1.81.0-gcc-12.3.0/include/boost/heap/fibonacci_heap.hpp:715:20:  
/opt/wandbox/boost-1.81.0-gcc-12.3.0/include/boost/array.hpp:117:68: warning: array subscript 64 is above array bounds of 'boost::heap::detail::marked_heap_node<int>* [64]' [-Warray-bounds]  
  117 |             return BOOST_ASSERT_MSG( i < N, "out of range" ), elems[i];  
      |                                                               ~~~~~^  
```  
  
This will cause error when -Wextra -Werror is specified.

---

## Comment 5

> Username: cielavenir  
> Created at: 2024-11-09 11:08:08 UTC  
> Url: https://github.com/boostorg/heap/issues/44#issuecomment-2466174624  

I really don't know how to fix this properly, but the easiest fix would be adding BOOST_ENABLE_ASSERT_HANDLER_NORETURN, implemented in https://github.com/boostorg/assert/pull/40 .

---

## Comment 6

> Username: cielavenir  
> Created at: 2024-11-09 14:49:56 UTC  
> Url: https://github.com/boostorg/heap/issues/44#issuecomment-2466245745  

BOOST_ASSERT_HANDLER_IS_NORETURN flag addition is now merged, so for now it is ok, though it is unknown why gcc warns the original warning.
