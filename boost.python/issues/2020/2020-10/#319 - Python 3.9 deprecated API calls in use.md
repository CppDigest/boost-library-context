# #319 - Python 3.9 deprecated API calls in use [Closed]

> Username: jcpunk  
> Created at: 2020-10-14 18:46:52 UTC  
> Updated at: 2020-10-16 14:08:15 UTC  
> Closed at: 2020-10-16 14:08:15 UTC  
> Url: https://github.com/boostorg/python/issues/319  

Per the Python 3.9 release notes[1], `PyEval_CallFunction` is deprecated  
  
I believe `PyObject_CallFunction` is the preferred replacement[2].  
  
[1]  
https://docs.python.org/3/whatsnew/3.9.html#deprecated  
  
[2]  
https://docs.python.org/3/c-api/call.html?highlight=pyobject_callfunction#c.PyObject_CallFunction
