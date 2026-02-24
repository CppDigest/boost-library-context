# #209 - boost::process:child constructor segmentation fault when passed an empty string [Open]

> Username: bee-rock  
> Created at: 2021-05-31 17:25:25 UTC  
> Updated at: 2023-07-09 05:19:12 UTC  
> Url: https://github.com/boostorg/process/issues/209  

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

> Username: ArielSilver  
> Created at: 2023-07-09 05:19:11 UTC  
> Url: https://github.com/boostorg/process/issues/209#issuecomment-1627608814  

@klemens-morgenstern   
This is still happening.  
The line: `boost::process::child("");`  causes the following:  
![image](https://github.com/boostorg/process/assets/137321897/3660a514-a17c-4ea8-883d-c257b89faf6b)  
  
Of course such scenario should fail, but I don't think it should cause a segmentation fault.   
I think it should fail in the executer.hpp after a failed execv (In linux).  
Regarding Windows I will find a similar solution.   
  
Do you want me to create a pull request for this?
