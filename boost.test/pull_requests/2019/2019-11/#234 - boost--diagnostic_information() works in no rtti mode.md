# #234 boost::diagnostic_information() works in no rtti mode. [Closed]

> Username: ww898  
> Created at: 2019-11-11 14:58:24 UTC  
> Updated at: 2019-12-02 22:54:10 UTC  
> Closed at: 2019-11-12 17:02:55 UTC  
> Url: https://github.com/boostorg/test/pull/234  

boost::exception processes `BOOST_NO_TYPEID` and `BOOST_NO_RTTI` inside more flexible.

---

## Comment 1

> Username: ww898  
> Created_at: 2019-11-11 21:24:21 UTC  
> Url: https://github.com/boostorg/test/pull/234#issuecomment-552622662  

I see a lot of C++11 test failures. Is it normal?

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2019-11-11 21:26:29 UTC  
> Updated_at: 2019-11-11 21:26:44 UTC  
> Url: https://github.com/boostorg/test/pull/234#issuecomment-552623446  

Yes, the travis and appveyor need some rework so no worries

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2019-11-12 17:02:55 UTC  
> Url: https://github.com/boostorg/test/pull/234#issuecomment-552987664  

All good, in next, should be there for 1.72!  
Thanks!

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2019-12-02 22:54:10 UTC  
> Url: https://github.com/boostorg/test/pull/234#issuecomment-560816928  

In master

---
