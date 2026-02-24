# #76 - warning: control reaches end of non-void function [Closed]

> Username: alexeiz  
> Created at: 2024-11-25 05:28:34 UTC  
> Updated at: 2024-11-25 18:50:50 UTC  
> Closed at: 2024-11-25 18:50:48 UTC  
> Url: https://github.com/boostorg/leaf/issues/76  

### Version of Boost  
  
1.86.0  
  
### Actual and Expected Behavior  
  
The following program should compile without warnings:  
  
```cpp  
#include <boost/leaf.hpp>  
  
using namespace boost;  
  
enum class err1 {  
    e1,  
    e2,  
    e3,  
};  
  
leaf::result<int> foo(int val) {  
    if (val == 0)  
        return leaf::new_error(err1::e2);  
    return val;  
}  
  
int main(int argc, char * argv[]) {  
    auto _ = leaf::try_handle_some(  
        [=] -> leaf::result<void> {  
            BOOST_LEAF_CHECK(foo(argc - 1));  
            return {};  
        },  
        [](err1 e) -> leaf::result<void> {  
            return {};  
        }  
    );  
}  
```  
  
However the following warning is produced:  
  
```  
[build] In file included from .../boost-src/libs/leaf/include/boost/leaf/detail/all.hpp:15,  
[build]                  from .../boost-src/libs/leaf/include/boost/leaf.hpp:9,  
[build]                  from my/main.cpp:1:  
[build] .../boost-src/libs/leaf/include/boost/leaf/result.hpp: In member function ‘boost::leaf::result<T>::error_result::operator boost::leaf::result<U>() [with U = void; T = int]’:  
[build] .../boost-src/libs/leaf/include/boost/leaf/result.hpp:238:9: warning: control reaches end of non-void function [-Wreturn-type]  
[build]   238 |         }  
[build]       |         ^  
```  
  
### All relevant compiler information  
  
g++14 -std=c++23  -Wall -Wextra -Wno-unused-parameter

---

## Comment 1

> Username: zajo  
> Created at: 2024-11-25 18:50:48 UTC  
> Url: https://github.com/boostorg/leaf/issues/76#issuecomment-2498791634  

Thanks -- this should be fixed in the upcoming 1.87.0 release. You can download the beta to confirm, please reopen if needed.
