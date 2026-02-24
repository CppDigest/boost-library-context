# #304 - inconsistent numpy dtype in python and C++ [Open]

> Username: zhangguy  
> Created at: 2020-04-16 21:07:05 UTC  
> Updated at: 2020-04-16 21:11:41 UTC  
> Url: https://github.com/boostorg/python/issues/304  

Hi,  
I'm trying to use the boost numpy bindings. If I pass two ndarray with different dtype to C++, they don't preserve the correct dtype. Here is a simple example:  
dummy.cpp:  
```  
#include <iostream>  
#include <boost/python.hpp>  
#include <boost/python/numpy.hpp>  
  
namespace py = boost::python;  
namespace np = boost::python::numpy;  
  
void dummy(np::ndarray const& a, np::ndarray const& b) {  
  
    char const* at = py::extract<char const* >(py::str(a.get_dtype()));  
    char const* bt = py::extract<char const* >(py::str(b.get_dtype()));  
  
    std::cout << at << std::endl;  
    std::cout << bt << std::endl;  
}  
  
  
BOOST_PYTHON_MODULE(dummy) {  
   Py_Initialize(); // need initialize in BOOST_PYTHON_MODULE, or will get segmentation fault and core dump   
   np::initialize();  
   py::def("dummy", dummy);  
}  
```  
compile  
```  
g++ -lboost_python38 -lboost_numpy38 -L$CONDA_PREFIX/lib -I$CONDA_PREFIX/include -I$CONDA_PREFIX/include/python3.8 -fPIC --shared -o dummy.so dummy.cpp  
```  
run_dummpy.py  
```  
import numpy as np  
import dummy  
  
a = np.array([[1, 2, 3], [4, 5, 6]], dtype = np.float32)  
print("python", a.dtype)  
b = np.array([[1, 2, 3], [4, 5, 6]], dtype = np.double)  
print("python", b.dtype)  
  
dummy.dummy(a, b)  
```  
The output is inconsistent. The first time I run it by ```python run_dummy.py ``` it gives  
```  
python float32  
python float64  
__str__  
float64  
```  
If I re-run it ```python run_dummy.py ```, it gives  
```  
python float32  
python float64  
float64  
float64  
```  
Still not the right output.  
I'm using python 3.8.2, numpy 1.18.1, and boost 1.72.0. boost is installed from anaconda ```conda install -c conda-forge boost```  
Could you help? Thanks
