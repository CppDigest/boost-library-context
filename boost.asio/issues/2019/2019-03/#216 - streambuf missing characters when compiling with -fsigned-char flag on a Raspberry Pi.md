# #216 - streambuf missing characters when compiling with -fsigned-char flag on a Raspberry Pi [Closed]

> Username: mbroshi  
> Created at: 2019-03-20 16:17:44 UTC  
> Updated at: 2020-12-30 01:02:28 UTC  
> Closed at: 2020-12-30 01:02:27 UTC  
> Url: https://github.com/boostorg/asio/issues/216  

I have the following minimal working example of a `boost::asio::streambuf` dropping characters  
```c++  
#include <boost/asio.hpp>  
#include <iostream>  
  
int main() {  
    boost::asio::streambuf buf;  
    std::ostream out_header_and_message(&buf);  
    for(std::size_t c = 0; c < 129; c++)  
        out_header_and_message.put(255);  
    std::cout << buf.size() << std::endl; // 128 if compiled with -fsigned-char  
                                          // 129 if compiled without  
}  
```  
This only happens when compiled on a Raspberry Pi, and only when I add the -fsigned-char compiler flag. The first 128 characters are added as expected, but if the 129th character is 255, it is dropped.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:02:26 UTC  
> Url: https://github.com/boostorg/asio/issues/216#issuecomment-752291708  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#679](https://github.com/chriskohlhoff/asio/issues/679).
