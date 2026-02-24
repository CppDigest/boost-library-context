# #163 - Compilation failure with Python 3.2 [Open]

> Username: Lastique  
> Created at: 2017-10-05 15:19:28 UTC  
> Updated at: 2017-10-22 14:33:38 UTC  
> Url: https://github.com/boostorg/python/issues/163  

Boost 1.65.1 fails to compile on Debian Wheezy with Python 3.2:  
  
```  
libs/python/src/numpy/ndarray.cpp: In function 'boost::python::numpy::ndarray::bitflag boost::python::numpy::detail::numpy_to_bitflag(int)':  
libs/python/src/numpy/ndarray.cpp:25:11: error: 'NPY_ARRAY_C_CONTIGUOUS' was not declared in this scope  
libs/python/src/numpy/ndarray.cpp:26:11: error: 'NPY_ARRAY_F_CONTIGUOUS' was not declared in this scope  
libs/python/src/numpy/ndarray.cpp:27:11: error: 'NPY_ARRAY_ALIGNED' was not declared in this scope  
libs/python/src/numpy/ndarray.cpp:28:11: error: 'NPY_ARRAY_WRITEABLE' was not declared in this scope  
libs/python/src/numpy/ndarray.cpp: In function 'int boost::python::numpy::detail::bitflag_to_numpy(boost::python::numpy::ndarray::bitflag)':  
libs/python/src/numpy/ndarray.cpp:35:39: error: 'NPY_ARRAY_C_CONTIGUOUS' was not declared in this scope  
libs/python/src/numpy/ndarray.cpp:36:39: error: 'NPY_ARRAY_F_CONTIGUOUS' was not declared in this scope  
libs/python/src/numpy/ndarray.cpp:37:34: error: 'NPY_ARRAY_ALIGNED' was not declared in this scope  
libs/python/src/numpy/ndarray.cpp:38:36: error: 'NPY_ARRAY_WRITEABLE' was not declared in this scope  
libs/python/src/numpy/ndarray.cpp: In function 'boost::python::numpy::ndarray boost::python::numpy::detail::from_data_impl(void*, const boost::python::numpy::dtype&, const std::vector<long int>&, const std::vector<long int>&, const boos  
libs/python/src/numpy/ndarray.cpp:122:27: error: 'NPY_ARRAY_WRITEABLE' was not declared in this scope  
libs/python/src/numpy/ndarray.cpp:123:59: error: 'NPY_ARRAY_C_CONTIGUOUS' was not declared in this scope  
libs/python/src/numpy/ndarray.cpp:124:59: error: 'NPY_ARRAY_F_CONTIGUOUS' was not declared in this scope  
libs/python/src/numpy/ndarray.cpp:125:47: error: 'NPY_ARRAY_ALIGNED' was not declared in this scope  
libs/python/src/numpy/ndarray.cpp: In function 'boost::python::numpy::ndarray boost::python::numpy::array(const boost::python::api::object&)':  
libs/python/src/numpy/ndarray.cpp:246:45: error: 'NPY_ARRAY_ENSUREARRAY' was not declared in this scope  
libs/python/src/numpy/ndarray.cpp: In function 'boost::python::numpy::ndarray boost::python::numpy::array(const boost::python::api::object&, const boost::python::numpy::dtype&)':  
libs/python/src/numpy/ndarray.cpp:252:65: error: 'NPY_ARRAY_ENSUREARRAY' was not declared in this scope  
libs/python/src/numpy/ndarray.cpp:253:1: warning: control reaches end of non-void function [-Wreturn-type]  
libs/python/src/numpy/ndarray.cpp: In function 'boost::python::numpy::ndarray boost::python::numpy::array(const boost::python::api::object&)':  
libs/python/src/numpy/ndarray.cpp:247:1: warning: control reaches end of non-void function [-Wreturn-type]  
```

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-10-05 15:22:02 UTC  
> Url: https://github.com/boostorg/python/issues/163#issuecomment-334499291  

What NumPy version does Debian Wheezy provide ? (Looks like it might be older than the required version 1.7.)

---

## Comment 2

> Username: Lastique  
> Created at: 2017-10-05 15:27:00 UTC  
> Url: https://github.com/boostorg/python/issues/163#issuecomment-334500902  

`apt-cache` shows 1.6.2.  
  
Did the requirement change since 1.64?

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2017-10-05 15:29:31 UTC  
> Url: https://github.com/boostorg/python/issues/163#issuecomment-334501741  

Yes, but someone just submitted a patch for backward compatibility. See https://github.com/boostorg/python/pull/162

---

## Comment 4

> Username: Lastique  
> Created at: 2017-10-05 15:31:44 UTC  
> Url: https://github.com/boostorg/python/issues/163#issuecomment-334502480  

Thanks. You commented that the patch is dangerous though. Is it valid?

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2017-10-05 16:05:41 UTC  
> Url: https://github.com/boostorg/python/issues/163#issuecomment-334512912  

Well, it "backports" a slice of an API that Boost.Python requires, which seems fair. For the rest, see the review there (and the CI updates).
