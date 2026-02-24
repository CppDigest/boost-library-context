# #72 - Cannot build on macos [Closed]

> Username: upsilona  
> Created at: 2023-02-26 14:35:34 UTC  
> Updated at: 2023-03-02 15:10:19 UTC  
> Closed at: 2023-03-02 07:12:38 UTC  
> Url: https://github.com/boostorg/redis/issues/72  

Hello everyone,  
  
I'm in the process of trying boot::redis (master, latest commit), unfortunately I'm facing build errors on macos (no errors on Linux),  
  
```  
include/boost/redis/adapter/detail/adapters.hpp:54:15: error: variable has incomplete type 'const void'  
   auto const res = std::from_chars(sv.data(), sv.data() + std::size(sv), d);  
```  
  
Looking at the `charconv` header I can see the function is deleted by default, and overrided with several types later,  
  
```  
void from_chars(const char*, const char*, bool, int = 10) = delete;  
```  
  
I'm not sure to understand what's wrong here, anyone facing this issue ? Did Boost::redis has already been tested on MacOS ?  
  
I'm using Macos, Darwin-22.3.0 (arm64), AppleClang 14.0.0.14000029, Boost 1.81.0,  
  
Regards,

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-02-26 15:13:41 UTC  
> Updated at: 2023-02-26 16:28:27 UTC  
> Url: https://github.com/boostorg/redis/issues/72#issuecomment-1445386009  

Can you show me the code you are trying to build?

---

## Comment 2

> Username: upsilona  
> Created at: 2023-02-26 16:29:08 UTC  
> Url: https://github.com/boostorg/redis/issues/72#issuecomment-1445402790  

Here is the code I'm trying to build   
  
```  
#include <iostream>  
  
#include <boost/asio.hpp>  
#include <boost/redis.hpp>  
  
int main(int argc, char **argv) {  
      
    std::string host = "127.0.0.1";  
    std::string port = "6379";  
      
    boost::redis::request request;  
    request.push("HELLO", 3);  
    request.push("PING", "Hello world!");  
    request.push("QUIT");  
      
    boost::redis::response<boost::redis::ignore_t, std::string, boost::redis::ignore_t> response;  
      
    return 0;  
}  
```  
  
NB : I'm linking this code with a CMake target I previsouly created, in order to compile `<boost/redis/src.hpp>`

---

## Comment 3

> Username: mzimbres  
> Created at: 2023-02-26 16:44:59 UTC  
> Url: https://github.com/boostorg/redis/issues/72#issuecomment-1445405860  

I can build this code with clang++-14 on linux, both c++ 17 and c++20. Currently I have no CI setup for macos so I would have to look around to set one  
  
It is very strange that code is causing the problem, the variables `host`, `port`, `request` and `response` are all independent from each other, can you reduce it even further?  
  
The line the error is occurring is pointing to an unrelated function  
  
```cpp  
inline  
void boost_redis_from_bulk(double& d, std::string_view sv, system::error_code& ec)  
{  
   auto const res = std::from_chars(sv.data(), sv.data() + std::size(sv), d);  
   if (res.ec != std::errc())  
      ec = redis::error::not_a_double;  
}  
```  
  
I have to think more.

---

## Comment 4

> Username: upsilona  
> Created at: 2023-02-26 17:41:19 UTC  
> Url: https://github.com/boostorg/redis/issues/72#issuecomment-1445417499  

Indeed, simply having the redis header is enough to fail,  
  
This doesn't compile :  
  
```  
//#include <iostream>  
  
//#include <boost/asio.hpp>  
#include <boost/redis.hpp>  
  
int main(int argc, char **argv) {  
      
//    std::string host = "127.0.0.1";  
//    std::string port = "6379";  
//  
//    boost::redis::request request;  
//    request.push("HELLO", 3);  
//    request.push("PING", "Hello world!");  
//    request.push("QUIT");  
//  
//    boost::redis::response<boost::redis::ignore_t, std::string, boost::redis::ignore_t> response;  
      
    return 0;  
}  
  
```

---

## Comment 5

> Username: upsilona  
> Created at: 2023-02-27 21:39:31 UTC  
> Url: https://github.com/boostorg/redis/issues/72#issuecomment-1447135886  

Apparently apple-clang doesn't support `from_chars` with floating point (it's working with integer), see [this link](https://www.reddit.com/r/cpp_questions/comments/zg6qlt/from_chars_and_floatingpoint_issue/) for further info

---

## Comment 6

> Username: mzimbres  
> Created at: 2023-02-28 08:11:55 UTC  
> Url: https://github.com/boostorg/redis/issues/72#issuecomment-1447748383  

Thanks for the information. I am considering using my old implementation for clang < 15. You can either try to comment out the code that is not compiling or replace it with   
  
```cpp  
#include <boost/spirit/include/qi.hpp>  
#include <boost/spirit/home/x3.hpp>  
   
inline  
auto parse_double(  
   char const* data,  
   std::size_t size,  
   boost::system::error_code& ec) -> double  
{  
   static constexpr boost::spirit::x3::real_parser<double> p{};  
   double ret = 0;  
   if (!parse(data, data + size, p, ret))  
      ec = error::not_a_double;  
  
   return ret;  
}  
  
inline  
void from_bulk(  
   double& d,  
   std::string_view sv,  
   boost::system::error_code& ec)  
void from_bulk(double& d, std::string_view sv, boost::system::error_code& ec)  
{  
   d = parse_double(sv.data(), sv.size(), ec);  
}  
```

---

## Comment 7

> Username: mzimbres  
> Created at: 2023-02-28 20:58:57 UTC  
> Updated at: 2023-02-28 21:22:11 UTC  
> Url: https://github.com/boostorg/redis/issues/72#issuecomment-1448905056  

To reproduce on linux  
  
`clang++-14 examples/cpp17_intro.cpp -I ./include/ -I /opt/boost_1_81_0/include/ -stdlib=libc++ -lc++abi -std=c++17`  
  
I will add libc++ to the build pipeline. The needed debian packages are `libc++-14-dev` and `libc++abi-14-dev`

---

## Comment 8

> Username: upsilona  
> Created at: 2023-03-02 15:10:18 UTC  
> Url: https://github.com/boostorg/redis/issues/72#issuecomment-1452029279  

Build is now OK on macos for me, thanks a lot !!
