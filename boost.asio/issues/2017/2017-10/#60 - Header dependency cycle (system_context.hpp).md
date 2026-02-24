# #60 - Header dependency cycle (system_context.hpp) [Closed]

> Username: djarek  
> Created at: 2017-10-28 05:44:51 UTC  
> Updated at: 2017-12-02 08:16:01 UTC  
> Closed at: 2017-12-02 08:16:01 UTC  
> Url: https://github.com/boostorg/asio/issues/60  

This does not compile:  
```  
#include <boost/asio/system_context.hpp>  
  
int main() {  
    return 0;  
}  
```  
  
```boost/asio/system_context.hpp``` includes ```boost/asio/system_executor.hpp``` which uses ```system_contex``` in its implementation file (```boost/asio/impl/system_context.hpp```).

---

## Comment 1

> Username: chriskohlhoff  
> Created at: 2017-12-02 08:16:01 UTC  
> Url: https://github.com/boostorg/asio/issues/60#issuecomment-348676697  

Fixed on develop.
