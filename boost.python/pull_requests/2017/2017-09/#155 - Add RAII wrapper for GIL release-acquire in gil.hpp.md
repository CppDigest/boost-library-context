# #155 Add RAII wrapper for GIL release/acquire in gil.hpp [Closed]

> Username: jzwinck  
> Created at: 2017-09-12 07:09:12 UTC  
> Updated at: 2018-08-17 08:00:57 UTC  
> Closed at: 2018-08-17 08:00:57 UTC  
> Url: https://github.com/boostorg/python/pull/155  

Taken from my old Trac ticket: https://svn.boost.org/trac10/ticket/7836 which said:  
  
This ticket follows a discussion I started on the Python C++ SIG mailing list (which in turn followed a brief topic on the Boost developers list).  
  
I submit for inclusion in Boost.Python a pair of classes for releasing and acquiring the Python Global Interpreter Lock (GIL) using RAII semantics.  
  
This code (with different naming conventions but the same logic) has been used successfully by me "in production" and the releaser class is inspired by several examples online that others have until now copy-pasted when needed.

---

## Comment 1

> Username: jzwinck  
> Created_at: 2018-08-17 08:00:57 UTC  
> Url: https://github.com/boostorg/python/pull/155#issuecomment-413789359  

Closing due to inactivity.

---
