# #162 - Memory leak in condition variable timed wait [Closed]

> Username: savinz  
> Created at: 2017-12-25 18:49:37 UTC  
> Updated at: 2018-03-25 18:37:24 UTC  
> Closed at: 2017-12-27 08:50:48 UTC  
> Url: https://github.com/boostorg/fiber/issues/162  

Hi,  
  
the following test results in a memory leak (tested with valgrind, boost 1.66.0, gcc 6.3.0, linux x64):  
  
```c++  
#include <boost/fiber/all.hpp>                                                                                                                
#include <chrono>  
                                                                                                                                                 
using namespace std::chrono_literals;                                             
using namespace boost;                                                            
                                                                                  
fibers::mutex mtx;                                                                
fibers::condition_variable cv;                                                    
                                                                                  
void test() {                                                                                 
    fibers::async([]{                                                             
        std::unique_lock<fibers::mutex> lock(mtx);                                
        //fibers::async([]{ cv.notify_one(); });                                  
        cv.wait_for(lock, 1s);                                                    
    });                                                                                                                                             
}                                                                                 
```  
  
The issue seems to be in missing intrusive_ptr_release (context) in case (cv) wait_for time-out.  
No memory is leaked if wait_for completes without time-out. Other synchronization objects  
have the same issue.

---

## Comment 1

> Username: olk  
> Created at: 2017-12-27 08:50:48 UTC  
> Url: https://github.com/boostorg/fiber/issues/162#issuecomment-354078055  

ty

---

## Comment 2

> Username: mumumu  
> Created at: 2018-03-25 17:30:21 UTC  
> Url: https://github.com/boostorg/fiber/issues/162#issuecomment-375987962  

> remove use-coutner increment for timed wait ops.   
  
s/increment/decrement/ ?  
  
`intrusive_ptr_release` method calls `std::atomic::fetch_sub`  
  
https://github.com/boostorg/fiber/blob/c8f6ddc49704660df654e88aed56f79d98a1b3d7/include/boost/fiber/algo/algorithm.hpp#L55-L62

---

## Comment 3

> Username: olk  
> Created at: 2018-03-25 18:37:24 UTC  
> Url: https://github.com/boostorg/fiber/issues/162#issuecomment-375992879  

`intrusive_ptr_release()` is already contained in algorithm.hpp
