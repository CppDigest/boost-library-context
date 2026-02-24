# #230 - Add method to numpy::dtype to set byteorder [Open]

> Username: fhriley  
> Created at: 2018-08-21 14:50:02 UTC  
> Updated at: 2018-08-21 14:52:01 UTC  
> Url: https://github.com/boostorg/python/issues/230  

I've taken a look at `numpy/dtype.hpp`, and there does not appear to be any way to set the byte order of the array being constructed. The workaround I am currently using (which requires adding the numpy C API to my build and including the headers in my source) is the following:  
  
```  
np::dtype scalarDtype = np::dtype::get_builtin<int16_t>();  
reinterpret_cast<PyArray_Descr*>(scalarDtype.ptr())->byteorder = '>';  
```  
  
So it should be as simple as adding a method to dtype to allow the user to set the byteorder on the underlying numpy struct.
