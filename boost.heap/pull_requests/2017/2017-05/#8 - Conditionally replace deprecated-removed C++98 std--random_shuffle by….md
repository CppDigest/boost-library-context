# #8 Conditionally replace deprecated/removed C++98 std::random_shuffle by… [Merged]

> Username: DanielaE  
> Created at: 2017-05-02 17:29:19 UTC  
> Updated at: 2017-12-29 10:42:19 UTC  
> Merged at: 2017-05-02 18:02:48 UTC  
> Closed at: 2017-05-02 18:02:48 UTC  
> Url: https://github.com/boostorg/heap/pull/8  

… a drop-in implementation.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-05-02 17:31:32 UTC  
> Url: https://github.com/boostorg/heap/pull/8#issuecomment-298704944  

Tested on msvc 9.0, 10.0, 12.0, 14.0 and 14.1 . Compiler versions 14.x define BOOST_NO_CXX98_NO_RANDOM_SHUFFLE when in C++17 mode, thereby triggering the new code paths.

---

## Comment 2

> Username: timblechmann  
> Created_at: 2017-05-02 18:03:10 UTC  
> Url: https://github.com/boostorg/heap/pull/8#issuecomment-298713560  

nice, thanks a lot!

---
