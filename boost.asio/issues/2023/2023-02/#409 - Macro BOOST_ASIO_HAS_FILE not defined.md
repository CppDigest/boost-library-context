# #409 - Macro BOOST_ASIO_HAS_FILE not defined [Open]

> Username: JRazek  
> Created at: 2023-02-16 15:30:08 UTC  
> Updated at: 2023-03-26 05:04:04 UTC  
> Url: https://github.com/boostorg/asio/issues/409  

I am trying to compile basic program:  
  
```cpp  
  
#include <boost/asio/basic_stream_file.hpp>  
#include <iostream>  
  
auto main() -> int { auto b = boost::asio::basic_stream_file{}; }  
```  
`g++ ./not_compiling.cpp -std=c++20 -lboost -o boost_testing`  
I receive following compilation error  
```  
$ g++ ./not_compiling.cpp -std=c++20 -lboost -o boost_testing  
./not_compiling.cpp: In function ‘int main()’:  
./not_compiling.cpp:5:38: error: ‘boost::asio’ has not been declared  
    5 | auto main() -> int { auto b = boost::asio::basic_stream_file{}; }  
```  
  
I believe that it is an issue with undefined macro `BOOST_ASIO_HAS_FILE`.  
  
I've tried installing boost from arch repo, compiling from source on `boost-1.81.0/1.80.0` branches with the same result.  
```  
extra/boost 1.81.0-3 [installed]  
    Free peer-reviewed portable C++ source libraries (development headers)  
```  
  
```  
g++ --version  
g++ (GCC) 12.2.1 20230201  
```  
  
Manually defining macro in `/usr/include/boost/asio/detail/config.hpp` did not help and generated another compilation errors.  
  
Now, why is that macro not defined? How can I fix it?

---

## Comment 1

> Username: Techmeology  
> Created at: 2023-02-27 19:02:48 UTC  
> Url: https://github.com/boostorg/asio/issues/409#issuecomment-1446899911  

I ran into the same issue (also on Arch). I was able to work around it by defining `BOOST_ASIO_HAS_IO_URING` and linking against `liburing`.

---

## Comment 2

> Username: Techmeology  
> Created at: 2023-03-26 04:58:58 UTC  
> Updated at: 2023-03-26 05:04:04 UTC  
> Url: https://github.com/boostorg/asio/issues/409#issuecomment-1483995960  

It turns out you should also give `BOOST_ASIO_DISABLE_EPOLL`, otherwise you can get `epoll re-registration: File exists [system:17]` under certain circumstances when forking.  
  
From [the revision history](https://think-async.com/Asio/asio-1.21.0/doc/asio/history.html#asio.history.asio_1_21_0):  
  
> The backend is disabled by default, and must be enabled by defining both ASIO_HAS_IO_URING and ASIO_DISABLE_EPOLL.  
>  
> Simply defining ASIO_HAS_IO_URING alone will enable the backend without using it for the existing I/O objects. This allows it to be used for I/O objects that require io_uring support, such as files.
