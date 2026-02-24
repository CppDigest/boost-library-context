# #14 - `boost/parameter/python.hpp` doesn't compile with Python 3 [Closed]

> Username: pdimov  
> Created at: 2018-10-13 17:11:37 UTC  
> Updated at: 2018-10-29 01:43:01 UTC  
> Closed at: 2018-10-29 01:43:01 UTC  
> Url: https://github.com/boostorg/parameter/issues/14  

I get  
  
```  
C:\boost-git\develop\boost/parameter/python.hpp(66): error C2440: 'initializing'  
: cannot convert from 'const char [28]' to 'Py_ssize_t'  
C:\boost-git\develop\boost/parameter/python.hpp(66): note: There is no context i  
n which this conversion is possible  
C:\boost-git\develop\boost/parameter/python.hpp(66): error C2440: 'initializing'  
: cannot convert from 'long' to 'const char *'  
C:\boost-git\develop\boost/parameter/python.hpp(66): note: Conversion from integ  
ral type to pointer type requires reinterpret_cast, C-style cast or function-sty  
le cast  
C:\boost-git\develop\boost/parameter/python.hpp(68): error C2039: 'ob_type': is  
not a member of '_typeobject'  
c:\python33\include\object.h(333): note: see declaration of '_typeobject'  
C:\boost-git\develop\boost/parameter/python.hpp(70): error C2039: 'ob_type': is  
not a member of '_typeobject'  
c:\python33\include\object.h(333): note: see declaration of '_typeobject'  
```

---

## Comment 1

> Username: eldiener  
> Created at: 2018-10-13 17:15:03 UTC  
> Url: https://github.com/boostorg/parameter/issues/14#issuecomment-429559044  

Can you give me what you have in your user-config.jam for Python ?

---

## Comment 2

> Username: pdimov  
> Created at: 2018-10-13 17:35:22 UTC  
> Url: https://github.com/boostorg/parameter/issues/14#issuecomment-429560505  

When I originally saw the error, I didn't have anything, so it autodetected 3.3. At this particular moment, I have  
  
```  
using python : 2.7 : C:/Python27 ;  
using python : 3.3 : C:/Python33 ;  
```  
  
and `b2 test python=2.7` works, `b2 test python=3.3` fails with the errors above. My guess is that the structure `PyTypeObject` has changed in Python 3.

---

## Comment 3

> Username: pdimov  
> Created at: 2018-10-13 17:43:47 UTC  
> Url: https://github.com/boostorg/parameter/issues/14#issuecomment-429561153  

It looks like  
  
```  
          PyObject_HEAD_INIT(NULL)  
          0,                                /* ob_size        */  
```  
  
needs to be  
  
```  
          PyVarObject_HEAD_INIT(NULL, 0)  
```  
  
but I know nothing about Python, so I could be wrong here.
