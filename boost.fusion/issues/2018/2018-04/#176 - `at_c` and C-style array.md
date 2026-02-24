# #176 - `at_c` and C-style array [Closed]

> Username: Flast  
> Created at: 2018-04-18 00:28:58 UTC  
> Updated at: 2018-04-18 16:44:13 UTC  
> Closed at: 2018-04-18 16:44:13 UTC  
> Url: https://github.com/boostorg/fusion/issues/176  

from developer ML: https://lists.boost.org/Archives/boost/2018/04/242048.php  
  
Since 1.66 release, following code isn't compile.  
  
```cpp  
#include <boost/fusion/sequence/intrinsic/at.hpp>  
#include <boost/fusion/container/vector.hpp>  
  
int main()  
{  
        boost::fusion::vector<int[3]> v;  
        boost::fusion::at_c<0>(v)[0] = 1;  
}   
```
