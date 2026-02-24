# #13 Replace deprecated/removed auto_ptr by unique_ptr. [Merged]

> Username: DanielaE  
> Created at: 2017-05-05 13:55:36 UTC  
> Updated at: 2018-01-01 12:59:20 UTC  
> Merged at: 2017-11-21 02:03:09 UTC  
> Closed at: 2017-11-21 02:03:09 UTC  
> Url: https://github.com/boostorg/assign/pull/13  

Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-05-05 13:57:03 UTC  
> Url: https://github.com/boostorg/assign/pull/13#issuecomment-299471358  

Compiles and passes assign's testsuite like vanilla `develop` with msvc 9.0, 10.0, 12.0 and 14.1 - the latter in C++17 mode which defines BOOST_NO_AUTO_PTR and thereby triggers the new code paths.

---
