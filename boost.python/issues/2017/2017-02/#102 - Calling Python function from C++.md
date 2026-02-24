# #102 - Calling Python function from C++ [Closed]

> Username: tfgsupport  
> Created at: 2017-02-01 12:02:33 UTC  
> Updated at: 2017-02-02 15:06:22 UTC  
> Closed at: 2017-02-02 15:06:22 UTC  
> Url: https://github.com/boostorg/python/issues/102  

Hi,  
  
Please can you help explain how a user defined C++ class instance can be passed as an argument to a Python function. I am able to pass an integer argument using the PyInt_FromLong function as below.  
  
```  
	int MyClass::getSquare( int x )  
	{  
		return pow( x, 2 );  
	}  
	BOOST_PYTHON_MODULE( Test )  
	{  
		class_<MyClass>( "MyClass" )  
			.def( "getSquare", &MyClass::getSquare )  
			;  
	}  
  
	PyObject * py_args_tuple = PyTuple_New(1);  
	PyObject * py_int;  
	py_int = PyInt_FromLong( 4 );  
	PyTuple_SetItem( py_args_tuple, 0, py_int );  
	PyObject_CallObject( pFun, py_args_tuple );  
  
        def getSquare( x ):  
                        // Create MyClass object myClass  
			print myClass.getSquare(4)  
  
```  
Thanks,  
  
Nilufar

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-02-01 12:46:19 UTC  
> Url: https://github.com/boostorg/python/issues/102#issuecomment-276648915  

Have you tried the examples in the Boost.Python tutorial ? The automatic conversion from C++ objects to the appropriate Python wrapper object is done when the Boost.Python call operator is invoked. That is, for this to work you shouldn't use the Python C API but really Boost.Python.  
Assuming you hold the function in a Boost.Python object `pFun`, you can simply call that with the class instance argument. E.g.  
  
```  
boost::python::object pFun = ...;  
MyClass &instance = ...;  
pFun(instance); // ... will do all the proper conversion, as long as MyClass is registered.  
```

---

## Comment 2

> Username: tfgsupport  
> Created at: 2017-02-02 14:45:54 UTC  
> Url: https://github.com/boostorg/python/issues/102#issuecomment-276976612  

Thankyou. That suggestion works. I amended my code to now hold the python file and the python method I retrieve from it in a boost::python::object. I can then invoke the function directly with my class instance.
