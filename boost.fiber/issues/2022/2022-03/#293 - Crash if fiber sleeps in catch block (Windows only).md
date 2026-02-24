# #293 - Crash if fiber sleeps in catch block (Windows only) [Open]

> Username: vgaraschuk  
> Created at: 2022-03-22 22:17:04 UTC  
> Updated at: 2022-03-22 22:17:04 UTC  
> Url: https://github.com/boostorg/fiber/issues/293  

If anything makes a fiber sleep in the 'catch' block (not by thread sleeping, but switching to another fiber), then, when it wakes up, it crashes on trying to switch back to the 'try' block (at least, according to the call stack). The issue is not reproducible on Linux.  
The following code crashes when built and run on Windows (tested with boost 1.67 and boost 1.76):  
  
```C++  
#include "boost/fiber/all.hpp"  
  
void func()  
{  
    try  
    {  
        throw std::logic_error("error");  
    }  
    catch (...)  
    {  
        boost::this_fiber::sleep_for(std::chrono::milliseconds(100));  
    }  
}  
  
int main()  
{  
    boost::fibers::fiber f1(func);  
    boost::fibers::fiber f2(func);  
  
    f1.join();  
    f2.join();  
  
    return 0;  
}  
```
