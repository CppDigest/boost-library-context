# #211 - fiber_specific_ptr leaks objects [Open]

> Username: rockeet  
> Created at: 2019-08-27 11:29:45 UTC  
> Updated at: 2019-10-15 03:38:30 UTC  
> Url: https://github.com/boostorg/fiber/issues/211  

## Description  
main fiber's fiber_specific_ptr is not correctly cleaned.  
  
## Test Code:  
```c++  
#include <boost/fiber/fiber.hpp>  
#include <boost/fiber/fss.hpp>  
#include <thread>  
  
void clean_fss_int(int* p) {  
    printf("clean int = %d\n", *p);  
}  
static boost::fibers::fiber_specific_ptr<int> fs0(clean_fss_int);  
static thread_local boost::fibers::fiber_specific_ptr<int> fs1(clean_fss_int);  
  
int main(int argc, char* argv[]) {  
    boost::fibers::fiber_specific_ptr<int> fs2(clean_fss_int);  
    auto func = [&](const char* name) {  
        printf("---- %s ----\n", name);  
        boost::fibers::fiber_specific_ptr<int> fs3(clean_fss_int);  
        if (fs0.get() == NULL) {  
            fs0.reset(new int(0));  
        }  
        if (fs1.get() == NULL) {  
            fs1.reset(new int(1));  
        }  
        if (fs2.get() == NULL) {  
            fs2.reset(new int(2));  
        }  
        if (fs3.get() == NULL) {  
            fs3.reset(new int(3));  
        }  
    };  
    std::thread thr(func, "thread");  
    thr.join();  
    boost::fibers::fiber fb(func, "fiber");  
    fb.join();  
    return 0;  
}  
```  
## Expected output:  
---- thread ----  
clean int = 3  
clean int = 0  
clean int = 1  
clean int = 2  
---- fiber ----  
clean int = 3  
clean int = 0  
clean int = 1  
clean int = 2  
  
## Actual output:  
---- thread ----  
clean int = 3  
clean int = 1  
---- fiber ----  
clean int = 3  
clean int = 0  
clean int = 1  
clean int = 2
