# #57 - unsynchronized_push does not compile while stack capacity is specified [Closed]

> Username: caizongchao  
> Created at: 2020-04-22 05:54:33 UTC  
> Updated at: 2020-06-03 15:29:45 UTC  
> Closed at: 2020-06-03 15:29:45 UTC  
> Url: https://github.com/boostorg/lockfree/issues/57  

The code below does not compile( boost 1.72, visual c++ 2019 community), the error message is:  
  
boost\boost\lockfree\stack.hpp(241,42): error C2440: '=': cannot convert from 'T *' to 'unsigned short'  
  
```  
#include <boost/lockfree/stack.hpp>  
  
int main()  
{  
    boost::lockfree::stack<int, boost::lockfree::capacity<256>> int_stack;  
  
    int_stack.unsynchronized_push(100);  
  
    return 0;  
}  
```
