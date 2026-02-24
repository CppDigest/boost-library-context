# #389 - Unexpected exit when use Visual Studio 2022 [Open]

> Username: linjiahao962889027  
> Created at: 2022-07-04 08:52:37 UTC  
> Updated at: 2022-07-04 08:52:37 UTC  
> Url: https://github.com/boostorg/python/issues/389  

I installed boost Python on Windows using vcpkg. And I successfully compiled my DLL file, but I don't know why Python always exits automatically after import the dll.  
This is my code (dllmain.cpp):  
```cpp  
// dllmain.cpp : 定义 DLL 应用程序的入口点。  
#include "pch.h"  
  
#include <iostream>  
#include <string>  
#define BOOST_PYTHON_STATIC_LIB  
#include <boost/python.hpp>  
  
using namespace boost::python;  
  
void test() {  
	std::cout << "Test" << std::endl;  
}  
  
BOOST_PYTHON_MODULE(Dll1)  
{  
	boost::python::def("test", test);  
}  
```  
![屏幕截图 2022-07-04 164552](https://user-images.githubusercontent.com/46292984/177118025-a30fc298-f14a-4cd3-add0-ea13fe2a61cf.png)  
![屏幕截图 2022-07-04 164750](https://user-images.githubusercontent.com/46292984/177119124-f13c3d88-4e5d-4a51-9ed9-0be0e6357d07.png)  
This is my Visual Studio 2022 Config:  
![屏幕截图 2022-07-04 164905](https://user-images.githubusercontent.com/46292984/177119173-80d57bec-6d9a-4d1f-b89a-c2cfcf08071b.png)  
![屏幕截图 2022-07-04 164926](https://user-images.githubusercontent.com/46292984/177119192-e7dd9407-5cae-43bb-afbd-3c1f0ae53874.png)  
![屏幕截图 2022-07-04 164952](https://user-images.githubusercontent.com/46292984/177119202-1e9bb984-2075-480f-b835-d09b2550f2ca.png)
