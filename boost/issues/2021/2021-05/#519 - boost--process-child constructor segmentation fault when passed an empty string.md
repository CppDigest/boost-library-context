# #519 - boost::process:child constructor segmentation fault when passed an empty string [Closed]

> Username: bee-rock  
> Created at: 2021-05-31 17:09:46 UTC  
> Updated at: 2021-05-31 17:26:06 UTC  
> Closed at: 2021-05-31 17:26:06 UTC  
> Url: https://github.com/boostorg/boost/issues/519  

You can demonstrate this on the Wandbox online compiler using gcc 11.1.0 and c++11:   
  
```  
# prog.cc  
#include <boost/process.hpp>  
#include <system_error>  
  
   
int main(){  
    try {  
        boost::process::child("");  
    } catch(const std::system_error& e) {  
    }  
}  
```  
  
```  
 g++ prog.cc -Wall -Wextra -I/opt/wandbox/boost-1.76.0/gcc-11.1.0/include -std=c++11  
   
Start  
Segmentation fault  
Finish  
 ```  
   
 The documentation on https://www.boost.org/doc/libs/1_76_0/doc/html/boost_process/tutorial.html#boost_process.tutorial.error_handling leads me to believe that this case should throw a std::system_error rather than segfault.

---

## Comment 1

> Username: bee-rock  
> Created at: 2021-05-31 17:26:06 UTC  
> Url: https://github.com/boostorg/boost/issues/519#issuecomment-851607740  

Closing in lieu of having the issue on the correct project: https://github.com/boostorg/process/issues/209
