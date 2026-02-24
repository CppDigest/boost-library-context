# #97 Fix for uClibc and gcc <= 4.8.2 [Closed]

> Username: joerg-krause  
> Created at: 2016-03-30 21:46:11 UTC  
> Updated at: 2016-03-31 23:13:45 UTC  
> Closed at: 2016-03-31 23:13:45 UTC  
> Url: https://github.com/boostorg/test/pull/97  

getchar() is defined as a macro in uClibc. This hits gcc bug 58952 [1] for all  
gcc version <= 4.8.2 and building boost/test fails:  
  
```  
./boost/test/impl/unit_test_main.ipp: In function 'int boost::unit_test::unit_test_main(boost::unit_test::init_unit_test_func, int, char**)':  
./boost/test/impl/unit_test_main.ipp:194:18: error: expected unqualified-id before '(' token  
```  
  
To allow building boost/test with uClibc based toolchains with gcc <= 4.8.2 use  
parenthesis for std::getchar.  
  
[1] https://gcc.gnu.org/bugzilla/show_bug.cgi?id=58952

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2016-03-30 21:53:33 UTC  
> Url: https://github.com/boostorg/test/pull/97#issuecomment-203652055  

Thanks, will merge as soon as 1.61 is released. Would you mind creating a ticket in https://svn.boost.org/trac/boost/ ?

---

## Comment 2

> Username: joerg-krause  
> Created_at: 2016-03-30 22:00:16 UTC  
> Url: https://github.com/boostorg/test/pull/97#issuecomment-203657092  

Done: https://svn.boost.org/trac/boost/ticket/12103

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2016-03-31 23:13:43 UTC  
> Url: https://github.com/boostorg/test/pull/97#issuecomment-204167155  

Applied into branch topic/trac-12103-gcc-bug-for-getchar . Thanks!

---
