# #57 - [Tuple] Brace-initialization fails when it should work [Closed]

> Username: ldionne  
> Created at: 2015-05-01 18:10:38 UTC  
> Updated at: 2015-05-03 19:22:52 UTC  
> Closed at: 2015-05-03 19:22:52 UTC  
> Url: https://github.com/boostorg/hana/issues/57  

The following snippet does not compile:  
  
``` cpp  
#include <string>  
#include <boost/hana/tuple.hpp>  
  
struct T { std::string s; };  
int main() {  
    boost::hana::_tuple<T> t{{"foo"}};  
}  
```
