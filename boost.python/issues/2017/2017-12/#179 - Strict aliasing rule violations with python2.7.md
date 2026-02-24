# #179 - Strict aliasing rule violations with python2.7 [Open]

> Username: djarek  
> Created at: 2017-12-03 22:19:06 UTC  
> Updated at: 2017-12-03 22:39:34 UTC  
> Url: https://github.com/boostorg/python/issues/179  

` -fno-strict-aliasing` must be specified for g++, due to the fact that the Py_* macros rely on UB (access through type punned pointers of incompatible types). This issue is not present in python3.  
  
Reference: http://legacy.python.org/dev/peps/pep-3123/  
  
Some of the warnings produced with g++-7.0.1:  
```  
gcc.compile.c++ bin.v2/libs/python/build/gcc-gnu-7/release/threadapi-pthread/threading-multi/object/class.o  
In file included from /usr/include/python2.7/Python.h:80:0,  
                 from ./boost/python/detail/wrap_python.hpp:151,  
                 from ./boost/python/detail/prefix.hpp:13,  
                 from libs/python/src/object/class.cpp:6:  
libs/python/src/object/class.cpp: In function ‘PyObject* boost::python::objects::static_data()’:  
/usr/include/python2.7/object.h:115:51: warning: dereferencing type-punned pointer will break strict-aliasing rules [-Wstrict-aliasing]  
 #define Py_TYPE(ob)             (((PyObject*)(ob))->ob_type)  
                                                   ^  
libs/python/src/object/class.cpp:211:11: note: in expansion of macro ‘Py_TYPE’  
           Py_TYPE(&static_data_object) = &PyType_Type;  
           ^  
libs/python/src/object/class.cpp: In function ‘boost::python::type_handle boost::python::objects::class_metatype()’:  
/usr/include/python2.7/object.h:115:51: warning: dereferencing type-punned pointer will break strict-aliasing rules [-Wstrict-aliasing]  
 #define Py_TYPE(ob)             (((PyObject*)(ob))->ob_type)  
                                                   ^  
libs/python/src/object/class.cpp:319:11: note: in expansion of macro ‘Py_TYPE’  
           Py_TYPE(&class_metatype_object) = &PyType_Type;  
           ^  
libs/python/src/object/class.cpp: In function ‘boost::python::type_handle boost::python::objects::class_type()’:  
/usr/include/python2.7/object.h:115:51: warning: dereferencing type-punned pointer will break strict-aliasing rules [-Wstrict-aliasing]  
 #define Py_TYPE(ob)             (((PyObject*)(ob))->ob_type)  
                                                   ^  
libs/python/src/object/class.cpp:473:11: note: in expansion of macro ‘Py_TYPE’  
           Py_TYPE(&class_type_object) = incref(class_metatype().get());  
```

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-12-03 22:34:09 UTC  
> Url: https://github.com/boostorg/python/issues/179#issuecomment-348822062  

Just for avoidance of doubt: can you clarify what this issue is about ? Are you suggesting any changes to Boost.Python code or documentation ? Something else ?

---

## Comment 2

> Username: djarek  
> Created at: 2017-12-03 22:39:33 UTC  
> Url: https://github.com/boostorg/python/issues/179#issuecomment-348822763  

I'm suggesting that the library is built with the flag `-fno-strict-aliasing` when toolset is gcc. Mentioning this in the docs is probably fine as well.
