# #196 - How can load python module in specified path in windows? [Closed]

> Username: MabinogiX  
> Created at: 2018-04-26 07:35:46 UTC  
> Updated at: 2018-04-26 13:21:04 UTC  
> Closed at: 2018-04-26 13:21:04 UTC  
> Url: https://github.com/boostorg/python/issues/196  

I can import a module in current directory, but I can not import it from other path. I have tried:  
```  
boost::python::import("../test1");  
boost::python::import("..\\test1");  
boost::python::import("D:\\test1");  
```  
And none of them work. Is there anyway I can do this?

---

## Comment 1

> Username: MabinogiX  
> Created at: 2018-04-26 07:44:33 UTC  
> Url: https://github.com/boostorg/python/issues/196#issuecomment-384545522  

Ok, I found the solution. Adding module path like this, then I can import my module:  
```  
bp::object path_apped = bp::import("sys").attr("path").attr("append");  
bp::str module_path = bp::str("module path");  
path_apped(module_path);  
```

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2018-04-26 13:21:04 UTC  
> Url: https://github.com/boostorg/python/issues/196#issuecomment-384638508  

I believe you misunderstand what "modules" are in Python, and how they are used. While there is a one-to-one mapping between modules and files, in Python you don't import modules referring to a file name. Likewise, `boost::python::import()` doesn't expect a filename / path, but a module name.  
Your solution though is correct: you modify the module search path such that the Python interpreter can find the module of the given name. See https://docs.python.org/3/reference/simple_stmts.html#import for details.
