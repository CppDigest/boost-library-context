# #261 - Cmake tests should be prefixed with ${PROJECT_NAME} and Python should be optional [Closed]

> Username: HDembinski  
> Created at: 2019-12-16 08:38:46 UTC  
> Updated at: 2019-12-19 12:47:43 UTC  
> Closed at: 2019-12-19 12:47:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/261  

> pdimov Today at 3:36 AM  
> @hdembinski at https://github.com/boostorg/histogram/blob/develop/test/CMakeLists.txt#L9, you have a test that is not prefixed with ${PROJECT_NAME}, not a good practice  
  
The Python tests should only execute when Python is available. Need something like  
```  
find_package(PythonInterpreter)  
if (PYTHON_FOUND)  
  add_test(....)  
endif()  
```
