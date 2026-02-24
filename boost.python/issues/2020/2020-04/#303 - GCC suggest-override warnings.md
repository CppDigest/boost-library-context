# #303 - GCC suggest-override warnings [Open]

> Username: EugeneZelenko  
> Created at: 2020-04-13 01:48:41 UTC  
> Updated at: 2020-05-30 22:16:38 UTC  
> Url: https://github.com/boostorg/python/issues/303  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
./boost/python/object/py_function.hpp:102:22: warning: ‘unsigned int boost::python::objects::full_py_function_impl<Caller, Sig>::max_arity() const [with Caller = _object* (*)(_object*, _object*); Sig = boost::mpl::vector1<void>]’ can be marked override [-Wsuggest-override]  
./boost/python/object/py_function.hpp:107:46: warning: ‘boost::python::detail::py_func_sig_info boost::python::objects::full_py_function_impl<Caller, Sig>::signature() const [with Caller = _object* (*)(_object*, _object*); Sig = boost::mpl::vector1<void>]’ can be marked override [-Wsuggest-override]  
./boost/python/object/py_function.hpp:36:15: warning: ‘PyObject* boost::python::objects::caller_py_function_impl<Caller>::operator()(PyObject*, PyObject*) [with Caller = boost::python::detail::caller<boost::python::tuple (*)(boost::python::api::object), boost::python::default_call_policies, boost::mpl::vector2<boost::python::tuple, boost::python::api::object> >; PyObject = _object]’ can be marked override [-Wsuggest-override]  
./boost/python/object/py_function.hpp:41:22: warning: ‘unsigned int boost::python::objects::caller_py_function_impl<Caller>::min_arity() const [with Caller = boost::python::detail::caller<boost::python::tuple (*)(boost::python::api::object), boost::python::default_call_policies, boost::mpl::vector2<boost::python::tuple, boost::python::api::object> >]’ can be marked override [-Wsuggest-override]  
./boost/python/object/py_function.hpp:46:46: warning: ‘boost::python::detail::py_func_sig_info boost::python::objects::caller_py_function_impl<Caller>::signature() const [with Caller = boost::python::detail::caller<boost::python::tuple (*)(boost::python::api::object), boost::python::default_call_policies, boost::mpl::vector2<boost::python::tuple, boost::python::api::object> >]’ can be marked override [-Wsuggest-override]  
./boost/python/object/py_function.hpp:62:15: warning: ‘PyObject* boost::python::objects::signature_py_function_impl<Caller, Sig>::operator()(PyObject*, PyObject*) [with Caller = _object* (*)(_object*, _object*); Sig = boost::mpl::vector2<_object*, _object*>; PyObject = _object]’ can be marked override [-Wsuggest-override]  
./boost/python/object/py_function.hpp:67:22: warning: ‘unsigned int boost::python::objects::signature_py_function_impl<Caller, Sig>::min_arity() const [with Caller = _object* (*)(_object*, _object*); Sig = boost::mpl::vector2<_object*, _object*>]’ can be marked override [-Wsuggest-override]  
./boost/python/object/py_function.hpp:72:46: warning: ‘boost::python::detail::py_func_sig_info boost::python::objects::signature_py_function_impl<Caller, Sig>::signature() const [with Caller = _object* (*)(_object*, _object*); Sig = boost::mpl::vector2<_object*, _object*>]’ can be marked override [-Wsuggest-override]  
./boost/python/object/py_function.hpp:92:15: warning: ‘PyObject* boost::python::objects::full_py_function_impl<Caller, Sig>::operator()(PyObject*, PyObject*) [with Caller = _object* (*)(_object*, _object*); Sig = boost::mpl::vector1<void>; PyObject = _object]’ can be marked override [-Wsuggest-override]  
./boost/python/object/py_function.hpp:97:22: warning: ‘unsigned int boost::python::objects::full_py_function_impl<Caller, Sig>::min_arity() const [with Caller = _object* (*)(_object*, _object*); Sig = boost::mpl::vector1<void>]’ can be marked override [-Wsuggest-override]  
</pre>  
  
Depends on boostorg/config#329.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2020-04-13 02:00:00 UTC  
> Url: https://github.com/boostorg/python/issues/303#issuecomment-612714944  

Not sure what to say. Boost.Python doesn't yet require C++11, so I'd suggest to simply not compile it with `-Wsuggest-override` for now.

---

## Comment 2

> Username: EugeneZelenko  
> Created at: 2020-04-13 02:31:52 UTC  
> Url: https://github.com/boostorg/python/issues/303#issuecomment-612721429  

Applications that use Boost.Python may be compiled with C++11 or newer. Also modern vwrsions of GCC and Clang  set C++11/14 by default.  
  
It's not very convenient to suppress warnings in each file that include Boost.Python.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2020-04-13 02:40:58 UTC  
> Url: https://github.com/boostorg/python/issues/303#issuecomment-612723266  

Can you suggest any alternative, short of making C++11 a requirement ?

---

## Comment 4

> Username: EugeneZelenko  
> Created at: 2020-04-13 04:16:22 UTC  
> Url: https://github.com/boostorg/python/issues/303#issuecomment-612742155  

Please take a look on boostorg/config#329 mentioned above. There will be macro that will handle both C++-11/newer and older versions.

---

## Comment 5

> Username: EugeneZelenko  
> Created at: 2020-04-13 22:43:51 UTC  
> Url: https://github.com/boostorg/python/issues/303#issuecomment-613133083  

`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 6

> Username: EugeneZelenko  
> Created at: 2020-04-27 16:46:45 UTC  
> Url: https://github.com/boostorg/python/issues/303#issuecomment-620103300  

Patch is in [python.txt](https://github.com/boostorg/python/files/4540939/python.txt).

---

## Comment 7

> Username: EugeneZelenko  
> Created at: 2020-05-30 22:16:38 UTC  
> Url: https://github.com/boostorg/python/issues/303#issuecomment-636392094  

Fixes are in #309.
