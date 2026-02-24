# #70 - cannot compile on master [Closed]

> Username: fleonis  
> Created at: 2015-12-14 12:37:11 UTC  
> Updated at: 2015-12-15 10:40:06 UTC  
> Closed at: 2015-12-15 09:03:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/70  

hello.  
i cannot compile this code on the master branche, only on the develop branche (it was so for a few month). now on the develop branche it compiles but crashes (terminated by signal SIGSEGV (Address boundary error)). i am on db88147  
  
``` c++  
#include <iostream>  
#include <boost/fiber/all.hpp>  
  
int main(int argc, char** argv) {  
        boost::fibers::fiber([](){std::cout << "hello from fiber" << std::endl;}).detach();  
        boost::this_fiber::yield();  
  
        return 0;  
}  
```

---

## Comment 1

> Username: olk  
> Created at: 2015-12-15 09:03:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/70#issuecomment-164692168  

please note that boost.fiber is a work-in-progress  
branch main is in the moment out-dated - you need branch develop (boost.context too)

---

## Comment 2

> Username: fleonis  
> Created at: 2015-12-15 10:40:06 UTC  
> Url: https://github.com/boostorg/fiber/issues/70#issuecomment-164722417  

ok. thank you for answer!
