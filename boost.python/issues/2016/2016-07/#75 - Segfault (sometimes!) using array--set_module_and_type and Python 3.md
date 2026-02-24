# #75 - Segfault (sometimes!) using array::set_module_and_type and Python 3 [Closed]

> Username: martyngigg  
> Created at: 2016-07-01 19:55:17 UTC  
> Updated at: 2017-07-20 18:07:04 UTC  
> Closed at: 2017-07-20 18:07:03 UTC  
> Url: https://github.com/boostorg/python/issues/75  

The following stripped down example demonstrates the issue.  
  
Module definition:  
  
``` c++  
#include <boost/python/def.hpp>  
#include <boost/python/module.hpp>  
#include <boost/python/numeric.hpp>  
  
using namespace boost::python;  
  
void pass_array(const numeric::array &) {  
  
}  
  
BOOST_PYTHON_MODULE(test_arrays) {  
  numeric::array::set_module_and_type("numpy", "ndarray");  
  def("pass_array", pass_array);  
}  
```  
  
Running the following produces a segfault but not for every run:  
  
``` bash  
echo; while [ $? == 0 ]; do PYTHONPATH=$PWD /usr/bin/python3 -c "from test_arrays import pass_array; import numpy as np; print('testing'); pass_array(np.arange(100.))"; done  
```  
  
The backtrace from a debug with of Python:  
  
``` bash  
 Program terminated with signal 11, Segmentation fault.  
#0  0x0000000000456c9a in dict_dealloc (mp=0x7f62ea20f488) at ../Objects/dictobject.c:1379  
(gdb) bt  
#0  0x0000000000456c9a in dict_dealloc (mp=0x7f62ea20f488) at ../Objects/dictobject.c:1379  
#1  0x000000000046355a in module_dealloc (m=0x7f62ebe40c28) at ../Objects/moduleobject.c:398  
#2  0x000000000062aca5 in meth_dealloc (m=0x7f62ea20f548) at ../Objects/methodobject.c:150  
#3  0x00007f62eaadf4f9 in ?? () from /lib/x86_64-linux-gnu/libc.so.6  
#4  0x00007f62eaadf545 in exit () from /lib/x86_64-linux-gnu/libc.so.6  
#5  0x00007f62eaac4ecc in __libc_start_main () from /lib/x86_64-linux-gnu/libc.so.6  
#6  0x000000000041deb9 in _start ()  
```  
  
I am not certain if it is related to this Python issue: http://bugs.python.org/issue17703

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2016-10-08 20:21:13 UTC  
> Url: https://github.com/boostorg/python/issues/75#issuecomment-252446273  

Just as a FYI: I have just merged a new NumPy C++ API extension into the development branch, and plan to release that with Boost 1.63. (It still lacks integration with Boost's own build system, as I'm mostly just working with Boost.Python's SCons-based build logic nowadays.)  
I encourage you to give that a try, as I also plan to deprecate the original "numeric" API.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2017-07-20 18:07:03 UTC  
> Url: https://github.com/boostorg/python/issues/75#issuecomment-316784934  

The `numeric` module has just been removed as it was obsoleted by the addition of Boost.NumPy. Please adjust your code to use the latter.
