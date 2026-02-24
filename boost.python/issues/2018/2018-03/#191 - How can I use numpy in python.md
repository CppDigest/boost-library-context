# #191 - How can I use numpy in python? [Closed]

> Username: MabinogiX  
> Created at: 2018-03-14 01:53:53 UTC  
> Updated at: 2018-03-15 15:13:20 UTC  
> Closed at: 2018-03-15 15:13:19 UTC  
> Url: https://github.com/boostorg/python/issues/191  

Hi,  
I am trying to send array in c++ to pythin with `boost::python::numpy`.   
I get a ndarray object and I can output it using `p::extract<char const *>(p::str(my_numpy_array))`. But I can not use it with python api.  
For example, I send the object to a python function, I get a result of -1.  
```  
#include<boost/python.hpp>  
#include<boost/python/numpy.hpp>  
  
namespace p = boost::python;  
namespace np = boost::python::numpy;  
  
#include<iostream>  
  
int main(int argc, char *argv[]){  
  
	long res;  
	Py_Initialize();  
	PyObject *pName, *pModule, *pDict, *pFunc;  
	PyObject *pArgs, *pValue;  
  
	pName = PyUnicode_FromString("mine");  
  
	pModule = PyImport_Import(pName);  
	Py_DECREF(pModule);  
  
	pFunc = PyObject_GetAttrString(pModule, "func1");  
	Py_DECREF(pFunc);  
  
	pArgs = PyTuple_New(1);  
  
	PyTuple_SetItem(pArgs, 0, PyLong_FromLong(5));  
  
	pValue = PyObject_CallObject(pFunc, pArgs);  
  
	res = PyLong_AsLong(pValue);  
	std::cout << "result: " << res << std::endl;  
}  
  
```  
python script:  
```  
def func1(a):  
	print(a)  
	print(type(a))  
	return 0;  
```  
With this code, I get:  
```  
5  
<class 'int'>  
result: 0  
```  
  
Then I change pArgs to boost::python::object.ptr(), everything goes wrong.  
```  
#include<boost/python.hpp>  
#include<boost/python/numpy.hpp>  
  
namespace p = boost::python;  
namespace np = boost::python::numpy;  
  
#include<iostream>  
  
int main(int argc, char *argv[]){  
  
	long res;  
	Py_Initialize();  
	PyObject *pName, *pModule, *pDict, *pFunc;  
	PyObject *pArgs, *pValue;  
  
	pName = PyUnicode_FromString("mine");  
  
	pModule = PyImport_Import(pName);  
	Py_DECREF(pModule);  
  
	pFunc = PyObject_GetAttrString(pModule, "func1");  
	Py_DECREF(pFunc);  
  
	np::initialize();  
	uint8_t mul_data[][4] = { { 1,2,3,4 },{ 5,6,7,8 },{ 1,3,5,7 } };  
	p::tuple shape = p::make_tuple(3, 2);  
	p::tuple stride = p::make_tuple(sizeof(uint8_t) * 2, sizeof(uint8_t));  
	np::dtype dt1 = np::dtype::get_builtin<uint8_t>();  
	np::ndarray np_data = np::from_data(mul_data, dt1,  
		p::make_tuple(3, 4),  
		p::make_tuple(4, 1),  
		p::object());  
  
	pValue = PyObject_CallObject(pFunc, np_data.ptr());  
  
	res = PyLong_AsLong(pValue);  
	std::cout << "result: " << res << std::endl;  
}  
```  
I get `result: -1`, which means the python interpreter even can not recognize the parameters.  
I have also tried `pValue = PyObject_CallObject(pFunc, p::make_tuple(3, 2).ptr())` and get the same result.  
But when I tried `pValue = PyObject_CallObject(pFunc, p::make_tuple(3).ptr())`, I get the result:  
```  
3  
<class 'int'>  
result: 0  
```  
So I can only pass the basic type to python?

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-03-14 02:25:36 UTC  
> Url: https://github.com/boostorg/python/issues/191#issuecomment-372883511  

You are using Python's C API unnecessarily. I have substituted that by actual Boost.Python equivalents, yielding  
```  
    Py_Initialize();  
    p::object main = p::import("__main__");  
    p::object global(main.attr("__dict__"));  
    p::str script("import sys, os.path\n"  
		  "sys.path.insert(0, '.')");  
    
    p::object result = p::exec(script, global, global);  
  
    p::object mine = p::import("mine");  
    p::object func = mine.attr("func1");  
	  
    np::initialize();  
    uint8_t mul_data[][4] = { { 1,2,3,4 },{ 5,6,7,8 },{ 1,3,5,7 } };  
    p::tuple shape = p::make_tuple(3, 2);  
    p::tuple stride = p::make_tuple(sizeof(uint8_t) * 2, sizeof(uint8_t));  
    np::dtype dt1 = np::dtype::get_builtin<uint8_t>();  
    np::ndarray np_data = np::from_data(mul_data, dt1,  
					p::make_tuple(3, 4),  
					p::make_tuple(4, 1),  
					p::object());  
    long res = p::extract<long>(func(np_data));  
    std::cout << "result: " << res << std::endl;  
```  
  
and this prints out the expected output:  
```  
[[1 2 3 4]  
 [5 6 7 8]  
 [1 3 5 7]]  
<class 'numpy.ndarray'>  
result: 0  
```

---

## Comment 2

> Username: MabinogiX  
> Created at: 2018-03-14 07:21:33 UTC  
> Url: https://github.com/boostorg/python/issues/191#issuecomment-372927298  

Thanks, it really helps!  
And by the way, is there any way to access the raw data array with pointer if I get a `boost::python::numpy::ndarray`?

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2018-03-14 11:47:34 UTC  
> Url: https://github.com/boostorg/python/issues/191#issuecomment-372993788  

I suppose you could do that using the `get_struct()` member function:  
https://github.com/boostorg/python/blob/develop/include/boost/python/numpy/ndarray.hpp#L41-L55

---

## Comment 4

> Username: MabinogiX  
> Created at: 2018-03-15 01:43:27 UTC  
> Updated at: 2018-03-15 01:44:10 UTC  
> Url: https://github.com/boostorg/python/issues/191#issuecomment-373232223  

Sorry, I don't understand. In my code, I get another `object` from `func(np_data)`, which is an ndarray. How could I get the array in C++?  
my c++ code:  
```  
Py_Initialize();  
p::object pModule = p::import("mine");  
p::object func1 = pModule.attr("func1");  
  
np::initialize();  
p::object data_obj;  
  
data_obj = func1();  
```  
my python code:  
```  
import numpy as np  
def func1():  
    return np.random.random((3,3))  
```  
I need the array in `data_obj`

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2018-03-15 02:03:06 UTC  
> Url: https://github.com/boostorg/python/issues/191#issuecomment-373235577  

Use the `np::array()` function to convert the `object` back into an `ndarray`:  
```  
np::ndarray a = np::array(data_obj);  
std::cout << "shape=(" << a.shape(0) << ',' << a.shape(1) << ')' << std::endl;  
```

---

## Comment 6

> Username: MabinogiX  
> Created at: 2018-03-15 15:11:22 UTC  
> Url: https://github.com/boostorg/python/issues/191#issuecomment-373410323  

Thank you very much! You are doing me a great service!  
I think it is worth to write it down in tutorial. I have been looking for ways to send array between c++ and python since Monday. I have searched the whole internet with google and find nothing. Many people use strange and complicated ways to send arrays.   
This can help lots of people!

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2018-03-15 15:13:19 UTC  
> Url: https://github.com/boostorg/python/issues/191#issuecomment-373411084  

Yeah, this may be good to document better.  
Meanwhile, I'm glad to see you got your code working, so I'll close this issue.  
Cheers !
