# #511 - numpy 2.x scalar types np.float32 and others are no longer convertible [Open]

> Username: dwpaley  
> Created at: 2026-02-18 18:28:58 UTC  
> Updated at: 2026-02-18 18:28:58 UTC  
> Url: https://github.com/boostorg/python/issues/511  

## Summary  
  
In NumPy 2.0, scalar types such as `np.float32`, `np.int32`, and `np.int64` no longer subclass Python's built-in `float` or `int` (per  
[NEP 51](https://numpy.org/neps/nep-0051-scalar-representation.html)). Boost.Python's built-in rvalue converters for C++ `double`, `float`, `int`, and `long` use `PyFloat_Check` / `PyLong_Check`, which return false for these NumPy scalars. The result is `ArgumentError: Python argument types did not match C++ signature` for any Boost.Python-wrapped function that previously accepted NumPy scalars via implicit conversion.  
  
`np.float64` is unaffected because it still subclasses Python `float`.  
  
## Reproducing  
  
Put the following in `test_module.cpp`:  
```  
#include <boost/python.hpp>  
  
double take_double(double x) { return x; }  
float take_float(float x) { return x; }  
int take_int(int x) { return x; }  
long take_long(long x) { return x; }  
  
BOOST_PYTHON_MODULE(test_module)  
{  
  using namespace boost::python;  
  def("take_double", take_double);  
  def("take_float", take_float);  
  def("take_int", take_int);  
  def("take_long", take_long);  
}  
```  
Then do:  
```  
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh  
bash Miniconda3-latest-Linux-x86_64.sh -b -p $PWD/mc3  
source mc3/etc/profile.d/conda.sh  
conda create -y -p $PWD/env -c conda-forge python=3.11 "numpy>=2.0" libboost-python-devel gxx_linux-64  
conda activate $PWD/env  
$CXX -shared -fPIC -o test_module.so test_module.cpp -Ienv/include/python3.11 -Ienv/include -Lenv/lib -lboost_python311 -Wl,-rpath,env/lib  
python -c "import numpy as np; import test_module; test_module.take_double(np.float32(1.5))"  
```  
  
The python script at the bottom of the issue gives some more types that don't convert.  
  
## Workaround  
  
Users can explicitly convert before passing to Boost.Python:  
  
```python  
func(float(np.float32(1.5)))   # works  
func(int(np.int32(42)))        # works  
func(np.float32(1.5).item())   # also works  
```  
I have implemented this in a few places and I'm sure many others have as well. See https://github.com/cctbx/cctbx_project/issues/1084  
  
  
## Longer python reproducer  
```  
import numpy as np  
import test_module  
  
# Show which numpy scalars still subclass builtins (the root cause)  
for np_type in [np.float64, np.float32, np.float16,  
                np.int64, np.int32, np.int16, np.int8]:  
  builtin = float if np.issubdtype(np_type, np.floating) else int  
  print("issubclass(%s, %s): %s" % (  
    np_type.__name__, builtin.__name__, issubclass(np_type, builtin)))  
print()  
  
scalar_values = [  
  np.float64(1.5),  
  np.float32(1.5),  
  np.float16(1.5),  
  np.int64(42),  
  np.int32(42),  
  np.int16(42),  
  np.int8(42),  
  np.uint64(42),  
  np.uint32(42),  
]  
  
functions = [  
  (test_module.take_double, "take_double"),  
  (test_module.take_float,  "take_float"),  
  (test_module.take_int,    "take_int"),  
  (test_module.take_long,   "take_long"),  
]  
  
for func, func_name in functions:  
  for scalar in scalar_values:  
    try:  
      result = func(scalar)  
      print("Pass", func_name, type(scalar))  
    except Exception as e:  
      print("Fail", func_name, type(scalar))  
```
