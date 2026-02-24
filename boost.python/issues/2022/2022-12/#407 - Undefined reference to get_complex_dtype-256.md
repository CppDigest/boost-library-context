# #407 - Undefined reference to get_complex_dtype<256> [Open]

> Username: hdu-sdlzx  
> Created at: 2022-12-13 09:05:16 UTC  
> Updated at: 2022-12-13 09:07:40 UTC  
> Url: https://github.com/boostorg/python/issues/407  

In src/numpy/dtype.cpp, the definition of get_float_dtype < LONGDOUBLE > and get_complex_dtype < 2 * LONGDOUBLE > lacks BOOST_NUMPY_DECL.  
  
Output of libs/python/test:  
capture-output ../bin.v2/libs/python/test/numpy\~dtype.test/gcc-10/release/debug-symbols-on/pch-off/python-3.9/threading-multi/visibility-hidden/numpy\~dtype  
====== BEGIN OUTPUT ======  
Traceback (most recent call last):  
  File "/root/rpmbuild/BUILD/boost_1_78_0/status/../libs/python/test/numpy/dtype.py", line 8, in <module>  
    import dtype_ext  
ImportError: /root/rpmbuild/BUILD/boost_1_78_0/bin.v2/libs/python/test/numpy~dtype.test/gcc-10/release/debug-symbols-on/pch-off/python-3.9/threading-multi/visibility-hidden/dtype_ext.so: undefined symbol: _ZN5boost6python5numpy6detail17get_complex_dtypeILi256EEENS1_5dtypeEv  
  
EXIT STATUS: 1  
====== END OUTPUT ======

---

## Comment 1

> Username: hdu-sdlzx  
> Created at: 2022-12-13 09:07:39 UTC  
> Url: https://github.com/boostorg/python/issues/407#issuecomment-1347985850  

Looks we could just use BUILTIN_FLOAT_DTYPE and BUILTIN_COMPLEX_DTYPE?
