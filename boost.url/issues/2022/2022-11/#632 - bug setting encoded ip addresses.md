# #632 - bug setting encoded ip addresses [Closed]

> Username: alandefreitas  
> Created at: 2022-11-29 23:46:19 UTC  
> Updated at: 2022-11-30 15:00:19 UTC  
> Closed at: 2022-11-30 15:00:19 UTC  
> Url: https://github.com/boostorg/url/issues/632  

It looks like it strips the middle of any 3 digit ip address node.....  
  
```cpp  
#include <boost/url/url.hpp>  
#include <boost/url/src.hpp>  
#include <iostream>  
  
int main(int argc, char** argv){  
  boost::urls::url myUrl;  
  myUrl.set_encoded_host("10.2.201.1");  
  std::cout << myUrl.buffer() << "\n"; //10.2.21.1  
}  
```
