# #50 - 64-byte alignment causes assertion in rbtree_best_fit [Closed]

> Username: tamboril  
> Created at: 2018-03-01 16:32:21 UTC  
> Updated at: 2021-05-11 12:49:16 UTC  
> Closed at: 2021-05-11 12:49:16 UTC  
> Url: https://github.com/boostorg/interprocess/issues/50  

Trying to make managed heap memory with a 64-byte alignment causes the assertion shown below the code.  
  
This asserts when `align` > 32 in boost 1.66.0 on CentOS7-64/gcc 7.2:  
  
```  
  
#include <boost/interprocess/mem_algo/rbtree_best_fit.hpp>  
#include <boost/interprocess/managed_heap_memory.hpp>  
  
using namespace ::std;  
using namespace boost::interprocess;  
  
int main(int, char *argv[])  
{  
    constexpr const static size_t align = 64;  
  
    typedef basic_managed_heap_memory<  
        char,  
        rbtree_best_fit<mutex_family, offset_ptr<void, long, unsigned long, align>, align>,  
        iset_index  
    > synchronized_managed_heap_t;  
  
    synchronized_managed_heap_t mem(10000); // asserts when align > 32  
  
    return 0;  
}  
```  
  
```  
boost/interprocess/mem_algo/rbtree_best_fit.hpp:410: void boost::interprocess::rbtree_best_fit<MutexFamily,  
VoidMutex, MemAlignment>::priv_add_segment(void*, boost::interprocess::rbtree_best_fit<MutexFamily, VoidMutex, MemAlignment>::size_type)  
 [with MutexFamily = boost::interprocess::mutex_family; VoidPointer = boost::interprocess::offset_ptr<void, long int, long unsigned int, 64>;  
long unsigned int MemAlignment = 64; boost::interprocess::rbtree_best_fit<MutexFamily, VoidMutex, MemAlignment>::size_type = long unsigned int]:  
Assertion `priv_end_block() == end_block' failed.  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-05-10 13:33:04 UTC  
> Url: https://github.com/boostorg/interprocess/issues/50#issuecomment-836705464  

Reviewing old bugs. Heap memory does not support overaligned types because the heap memory returned by operator new is not guaranteed to be suitable for over-aligned types. I will add this to the documentation.
