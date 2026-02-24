# #358 - numpy required for C++ library to work with python bindings [Open]

> Username: palu3492  
> Created at: 2021-04-01 15:51:38 UTC  
> Updated at: 2021-04-01 18:26:46 UTC  
> Url: https://github.com/boostorg/python/issues/358  

I've added Boost python bindings into a C++ library. Calling the initialization method of this C++ library from python will fail if numpy is not imported into python before importing my C++ library. There are no errors but the C++ library spits out it failed to initialize. Why would numpy be required in python for my boost bindings to work with this C++ library? See example below.  
  
C++  
  
```  
// Boost imports I'm using  
#include <boost/python.hpp>  
#include <boost/python/dict.hpp>  
#include <boost/python/object.hpp>  
using namespace boost;  
  
static bool InitializePy() {  
    // Call library initialize here  
}  
  
BOOST_PYTHON_MODULE(test_lib)  
{  
    python::def("initialize", InitializePy);  
}  
```  
  
Python  
  
```  
# import numpy  
import test_lib  
# Without importing numpy, this initialization will fail  
test_lib.initialize()  
```  
  
I can share more source if anyone would like a look but the way I see it, regardless of what else is being done in C++, numpy needing to be imported in python is odd to me.
