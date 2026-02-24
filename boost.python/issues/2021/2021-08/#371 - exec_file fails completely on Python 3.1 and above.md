# #371 - exec_file fails completely on Python 3.1 and above [Open]

> Username: hajokirchhoff  
> Created at: 2021-08-10 11:28:47 UTC  
> Updated at: 2021-08-10 11:28:47 UTC  
> Url: https://github.com/boostorg/python/issues/371  

When PY_VERSION_HEX >= 0x03010000, boost::python::exec_file passes an invalid string to Py_RunFile.  
  
The problem is that char* f points to a temporary buffer returned by the UTF conversion routine PyBytes_AsString, but this buffer is deallocated via Py_DECREF before f gets passed to Py_RunFile.  
  
I will create a pull request with a fix shortly.
