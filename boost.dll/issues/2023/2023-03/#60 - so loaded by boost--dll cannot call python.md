# #60 - so loaded by boost::dll cannot call python [Closed]

> Username: 155cannon  
> Created at: 2023-03-23 03:43:58 UTC  
> Updated at: 2023-05-29 06:08:57 UTC  
> Closed at: 2023-05-29 06:08:57 UTC  
> Url: https://github.com/boostorg/dll/issues/60  

Hello!  
  
I make a so in ubuntu, the so call a python file (C++ call python) with python-C api ,like:  
            Py_Initialize();  
            PyRun_SimpleString("import sys");  
            PyRun_SimpleString("sys.path.append('.')");	  
            PyImport_ImportModule("xxx");  
            .....  
           Py_Finalize();)  
It's OK.  
  
But when use boost::dll::import_alias() load the so, error occurs in the line "PyImport_ImportModule("xxx");", in the xxx.py, the import lines like "import open3d" occur "ImportError:  _ctypes.cpython-38-x86_64-linux-gnu.so: undefined sybol :pyfloat_type".   
It seems that import other 3rd modules (like open3d numpy) all occur errors.  
  
Thank everyone!

---

## Comment 1

> Username: apolukhin  
> Created at: 2023-05-29 06:08:57 UTC  
> Url: https://github.com/boostorg/dll/issues/60#issuecomment-1566603846  

There's a in-depth description of your problem here: https://stackoverflow.com/questions/67891197/ctypes-cpython-39-x86-64-linux-gnu-so-undefined-symbol-pyfloat-type-in-embedd  
  
Looks like you need to provide special flags while linking the so, and use `boost::dll::load_mode::rtld_global` while calling `boost::dll::import_alias()`
