# #218 Add missing return statement in numpy import [Merged]

> Username: WanzenBug  
> Created at: 2018-07-11 10:02:04 UTC  
> Updated at: 2018-07-11 20:54:05 UTC  
> Merged at: 2018-07-11 20:40:20 UTC  
> Closed at: 2018-07-11 20:40:21 UTC  
> Url: https://github.com/boostorg/python/pull/218  

This adds a missing return statement in the python3 specific  
import logic of boost.python.numpy.  
  
For python3 wrap_import_array() needs to return a pointer value.  
The import_array() macro only returns NULL in case of error. The  
missing return statement is UB, so the compiler can assume it does  
not happen. This means the compiler can assume the error branch  
is always taken, so import_array must always fail.  
  
This fixes #214 and #209

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2018-07-11 20:34:22 UTC  
> Url: https://github.com/boostorg/python/pull/218#issuecomment-404300939  

So just to make sure I understand your argument: you are saying that, because of the missing return statement, the compiler optimizes away the entire call to `import_array()` ?

---

## Comment 2

> Username: WanzenBug  
> Created_at: 2018-07-11 20:54:05 UTC  
> Url: https://github.com/boostorg/python/pull/218#issuecomment-404305904  

The argument is:  
1. `wrap_import_array()` is expanded by the preprocessor (`import_array()` is just a macro) so it looks like  
    ```c++  
    static void * wrap_import_array() {  
       if (_import_array() < 0) {  
          PyErr_Print();  
          PyErr_SetString(PyExc_ImportError, "numpy.core.multiarray failed to import");   
          return NUMPY_IMPORT_ARRAY_RETVAL;   
       }  
    }  
    ```  
    (taken from `/usr/include/numpy/__multiarray_api.h`)  
2. No `return <something>` is UB, so the compiler can assume it never happens -> _import_array() must always return < 0.  
3. `_import_array()` is also defined in the same `.h` file as `import_array()`, so it can get inlined.  
4. `_import_array()` is a long list of `if (condition) { return -1; }`, at the end it has `return 0;`. The compiler can assume this does not happen, as _import_array() must return < 0.  
5. (This part I'm not exactly sure about): The compiler knows it cannot reach the last `return 0;` so the last `if (condition)` must always be true. In our case this is exactly the check for endianess.

---
