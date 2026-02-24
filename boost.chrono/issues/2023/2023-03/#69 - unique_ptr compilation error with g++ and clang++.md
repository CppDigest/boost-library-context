# #69 - unique_ptr compilation error with g++ and clang++ [Closed]

> Username: erdemcaliskan  
> Created at: 2023-03-10 06:02:49 UTC  
> Updated at: 2023-03-10 06:28:50 UTC  
> Closed at: 2023-03-10 06:28:50 UTC  
> Url: https://github.com/boostorg/chrono/issues/69  

I'm can't compile my code when boost/chrono/chrono_io.hpp is included. I tried compilers:  
  
g++ (Ubuntu 12.2.0-3ubuntu1) 12.2.0  
  
and  
  
AMD clang version 14.0.6 (CLANG: AOCC_4.0.0-Build#434 2022_10_28) (based on LLVM Mirror.Version.14.0.6)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
InstalledDir: /opt/AMD/aocc-compiler-4.0.0/bin  
  
Error is:  
  
In file included from boost_1_80_0/aocc/include/boost/chrono/chrono_io.hpp:29:  
In file included from boost_1_80_0/aocc/include/boost/chrono/io_v1/chrono_io.hpp:26:  
In file included from boost_1_80_0/aocc/include/boost/chrono/detail/scan_keyword.hpp:22:  
boost_1_80_0/aocc/include/boost/move/unique_ptr.hpp:133:12: error: expected class member or base class name  
      : D(), m_p()  
           ^  
boost_1_80_0/aocc/include/boost/move/unique_ptr.hpp:137:12: error: expected class member or base class name  
      : D(), m_p(p)  
           ^  
boost_1_80_0/aocc/include/boost/move/unique_ptr.hpp:141:14: error: expected class member or base class name  
      : D(d1), m_p(p)  
             ^  
boost_1_80_0/aocc/include/boost/move/unique_ptr.hpp:146:34: error: expected class member or base class name  
      : D(::boost::forward<U>(d)), m_p(p)  
                                 ^

---

## Comment 1

> Username: mclow  
> Created at: 2023-03-10 06:12:44 UTC  
> Url: https://github.com/boostorg/chrono/issues/69#issuecomment-1463326239  

A small, self contained test case would be a big help here.

---

## Comment 2

> Username: mclow  
> Created at: 2023-03-10 06:15:43 UTC  
> Url: https://github.com/boostorg/chrono/issues/69#issuecomment-1463328226  

I tried this:  
```  
#include <boost/chrono/chrono_io.hpp>  
int main () {}  
```  
  
and it compiled w/o error. (apple clang 14, libc++, c++11, boost trunk)

---

## Comment 3

> Username: mclow  
> Created at: 2023-03-10 06:19:43 UTC  
> Url: https://github.com/boostorg/chrono/issues/69#issuecomment-1463330962  

Also compiled w/o error using boost 1.80.0 (same config as above)

---

## Comment 4

> Username: erdemcaliskan  
> Created at: 2023-03-10 06:28:48 UTC  
> Url: https://github.com/boostorg/chrono/issues/69#issuecomment-1463336800  

Thank you for the feedback, I'll do some checks on my side.
