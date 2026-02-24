# #479 - test/upcast.cpp does not compile since python 3.10 [Open]

> Username: e-kwsm  
> Created at: 2025-03-28 19:38:13 UTC  
> Updated at: 2025-03-28 19:38:13 UTC  
> Url: https://github.com/boostorg/python/issues/479  

revision: 3e7be69e1e405e1d5ddd232c69c024ee441592c5  
  
Since Python 3.10, compilation of test/upcast.cpp fails.  
  
https://github.com/boostorg/python/blob/3e7be69e1e405e1d5ddd232c69c024ee441592c5/test/upcast.cpp#L16-L17  
  
```console  
$ g++ -Iinclude -I/opt/miniconda3/envs/py310/include/python3.10 test/upcast.cpp  
In file included from /usr/include/boost/detail/lightweight_test.hpp:15,  
                 from ./test/upcast.cpp:7:  
./test/upcast.cpp: In function 'int main()':  
/opt/miniconda3/envs/py310/include/python3.10/object.h:133:33: error: lvalue required as unary '&' operand  
  133 | #define Py_REFCNT(ob) _Py_REFCNT(_PyObject_CAST_CONST(ob))  
      |                       ~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~  
./test/upcast.cpp:16:17: note: in expansion of macro 'Py_REFCNT'  
   16 |     BOOST_TEST(&Py_REFCNT(boost::python::upcast<PyObject>(&o)) == &Py_REFCNT(&o));  
      |                 ^~~~~~~~~  
/opt/miniconda3/envs/py310/include/python3.10/object.h:133:33: error: lvalue required as unary '&' operand  
  133 | #define Py_REFCNT(ob) _Py_REFCNT(_PyObject_CAST_CONST(ob))  
      |                       ~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~  
./test/upcast.cpp:16:68: note: in expansion of macro 'Py_REFCNT'  
   16 |     BOOST_TEST(&Py_REFCNT(boost::python::upcast<PyObject>(&o)) == &Py_REFCNT(&o));  
      |                                                                    ^~~~~~~~~  
/opt/miniconda3/envs/py310/include/python3.10/object.h:133:33: error: lvalue required as unary '&' operand  
  133 | #define Py_REFCNT(ob) _Py_REFCNT(_PyObject_CAST_CONST(ob))  
      |                       ~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~  
./test/upcast.cpp:17:17: note: in expansion of macro 'Py_REFCNT'  
   17 |     BOOST_TEST(&Py_REFCNT(boost::python::upcast<PyObject>(&y)) == &Py_REFCNT(&y));  
      |                 ^~~~~~~~~  
/opt/miniconda3/envs/py310/include/python3.10/object.h:133:33: error: lvalue required as unary '&' operand  
  133 | #define Py_REFCNT(ob) _Py_REFCNT(_PyObject_CAST_CONST(ob))  
      |                       ~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~  
./test/upcast.cpp:17:68: note: in expansion of macro 'Py_REFCNT'  
   17 |     BOOST_TEST(&Py_REFCNT(boost::python::upcast<PyObject>(&y)) == &Py_REFCNT(&y));  
      |                                                                    ^~~~~~~~~  
```  
  
```console  
$ g++ -Iinclude -I/usr/include/python3.13 test/upcast.cpp  
In file included from /usr/include/boost/detail/lightweight_test.hpp:15,  
                 from test/upcast.cpp:7:  
test/upcast.cpp: In function 'int main()':  
/usr/include/python3.13/object.h:330:34: error: lvalue required as unary '&' operand  
  330 | #  define Py_REFCNT(ob) Py_REFCNT(_PyObject_CAST(ob))  
      |                         ~~~~~~~~~^~~~~~~~~~~~~~~~~~~~  
test/upcast.cpp:16:17: note: in expansion of macro 'Py_REFCNT'  
   16 |     BOOST_TEST(&Py_REFCNT(boost::python::upcast<PyObject>(&o)) == &Py_REFCNT(&o));  
      |                 ^~~~~~~~~  
/usr/include/python3.13/object.h:330:34: error: lvalue required as unary '&' operand  
  330 | #  define Py_REFCNT(ob) Py_REFCNT(_PyObject_CAST(ob))  
      |                         ~~~~~~~~~^~~~~~~~~~~~~~~~~~~~  
test/upcast.cpp:16:68: note: in expansion of macro 'Py_REFCNT'  
   16 |     BOOST_TEST(&Py_REFCNT(boost::python::upcast<PyObject>(&o)) == &Py_REFCNT(&o));  
      |                                                                    ^~~~~~~~~  
/usr/include/python3.13/object.h:330:34: error: lvalue required as unary '&' operand  
  330 | #  define Py_REFCNT(ob) Py_REFCNT(_PyObject_CAST(ob))  
      |                         ~~~~~~~~~^~~~~~~~~~~~~~~~~~~~  
test/upcast.cpp:17:17: note: in expansion of macro 'Py_REFCNT'  
   17 |     BOOST_TEST(&Py_REFCNT(boost::python::upcast<PyObject>(&y)) == &Py_REFCNT(&y));  
      |                 ^~~~~~~~~  
/usr/include/python3.13/object.h:330:34: error: lvalue required as unary '&' operand  
  330 | #  define Py_REFCNT(ob) Py_REFCNT(_PyObject_CAST(ob))  
      |                         ~~~~~~~~~^~~~~~~~~~~~~~~~~~~~  
test/upcast.cpp:17:68: note: in expansion of macro 'Py_REFCNT'  
   17 |     BOOST_TEST(&Py_REFCNT(boost::python::upcast<PyObject>(&y)) == &Py_REFCNT(&y));  
      |                                                                    ^~~~~~~~~  
```  
  
This is because [`Py_REFCNT()` is changed to the inline static function](https://docs.python.org/3.10/c-api/structures.html#c.Py_REFCNT).  
  
Unfortunately, the CI images use python upto 3.8, so the issue has not been found.
