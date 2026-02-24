# #51 replace deprecated auto_ptr by unique_ptr [Closed]

> Username: DanielaE  
> Created at: 2017-05-18 15:55:27 UTC  
> Updated at: 2017-12-02 07:32:59 UTC  
> Closed at: 2017-12-02 07:32:59 UTC  
> Url: https://github.com/boostorg/asio/pull/51  

Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-05-18 15:57:46 UTC  
> Url: https://github.com/boostorg/asio/pull/51#issuecomment-302449027  

Compiles and passes testsuite with msvc 9.0, 10.0, 12.0 and 14.1. Compiler versions 14.x have no std::auto_ptr when in a mode 'later than C++14'.

---

## Comment 2

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:32:59 UTC  
> Url: https://github.com/boostorg/asio/pull/51#issuecomment-348674866  

All auto_ptr instances are now gone from develop.

---
