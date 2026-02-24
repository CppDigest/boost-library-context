# #177 - Infinite loop in streambuf iterator manipulation [Closed]

> Username: iceboy233  
> Created at: 2018-12-10 00:20:01 UTC  
> Updated at: 2018-12-19 02:04:44 UTC  
> Closed at: 2018-12-19 02:04:44 UTC  
> Url: https://github.com/boostorg/asio/issues/177  

Reproduction:  
  
```  
#include <boost/asio.hpp>  
  
int main() {  
    boost::asio::streambuf buffer;  
    buffer.prepare(1);  
    buffer.commit(1);  
    auto iter = buffers_begin(buffer.data());  
    iter + 1 - 1;  
}  
```  
  
This will lead to infinite loop, in `g++ (Debian 6.3.0-18+deb9u1) 6.3.0 20170516` with `boost 1.68`, when optimization is disabled. This works fine in MSVC in Windows.

---

## Comment 1

> Username: iceboy233  
> Created at: 2018-12-10 19:02:38 UTC  
> Url: https://github.com/boostorg/asio/issues/177#issuecomment-445932061  

Also confirmed the problem in gcc 7 and 8.  
  
clang 4, 5 and 7 don't have the problem.

---

## Comment 2

> Username: iceboy233  
> Created at: 2018-12-19 02:04:42 UTC  
> Url: https://github.com/boostorg/asio/issues/177#issuecomment-448442735  

ok, the problem is `buffer.data()` is returning a temporary object...
