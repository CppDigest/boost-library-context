# #70 - /test/exec.cpp - it is wrong to call PyImport_AppendInittab() after Py_Initialize() [Closed]

> Username: vmurashev  
> Created at: 2016-05-31 05:58:04 UTC  
> Updated at: 2016-09-29 03:46:25 UTC  
> Closed at: 2016-09-29 03:46:25 UTC  
> Url: https://github.com/boostorg/python/issues/70  

The test in /test/exec.cpp looks like   
1) Py_Initialize() is called in main()  
2) far lately in exec_test() PyImport_AppendInittab() is called to inject module 'embedded_hello'  
## It is wrong and doesn't work in python3.5  
  
According to to discussion in python.devel mailing list ( see http://permalink.gmane.org/gmane.comp.python.devel/157983 ) it is wrong to call PyImport_AppendInittab() after Py_Initialize()  
  
Any embedded module injected by PyImport_AppendInittab() and after  Py_Initialize() can't be imported in python3.5. Also there is a comment in python sources confirming this fact:  
## https://github.com/python/cpython/blob/f680b517e2701e9a3859afb62628a46eccdce17c/Python/import.c#L2153  
## Comment essence:  
  
API for embedding applications that want to add their own entries  
to the table of built-in modules.  This should normally be called  
_before_ Py_Initialize().  When the table resize fails, -1 is  
returned and the existing table is unchanged.

---

## Comment 1

> Username: vmurashev  
> Created at: 2016-09-24 14:52:29 UTC  
> Url: https://github.com/boostorg/python/issues/70#issuecomment-249368638  

patch suggested - see pull request 88

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2016-09-29 03:46:25 UTC  
> Url: https://github.com/boostorg/python/issues/70#issuecomment-250363546  

Merged, thanks.
