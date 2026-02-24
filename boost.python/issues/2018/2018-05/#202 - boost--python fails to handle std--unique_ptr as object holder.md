# #202 - boost::python fails to handle std::unique_ptr as object holder [Open]

> Username: rhaschke  
> Created at: 2018-05-13 15:22:21 UTC  
> Updated at: 2024-08-30 08:01:30 UTC  
> Url: https://github.com/boostorg/python/issues/202  

In ticket https://svn.boost.org/trac10/ticket/7711, 5 years ago, somebody complained that `std::unique_ptr` cannot be used as an object holder in boost::python. Although the ticket was closed, I still have the problem (in boost 1.58). Is there a solution available?  
  
```cpp  
#include <boost/python.hpp>  
#include <memory>  
  
struct Foo {};  
  
BOOST_PYTHON_MODULE(foo) {  
    // This compiles fine.  
    boost::python::class_<  
          Foo  
        , std::auto_ptr<Foo>  
        , boost::noncopyable  
        >("Foo1", boost::python::no_init)  
        ;  
  
    // This doesn't compile.  
    boost::python::class_<  
          Foo  
        , std::unique_ptr<Foo>  
        , boost::noncopyable  
        >("Foo2", boost::python::no_init)  
        ;  
}  
```  
  
Compile error (`std::unique_ptr` misses copy constructor):  
`usr/include/boost/python/object/make_ptr_instance.hpp:24:38:   
error: use of deleted function ‘std::unique_ptr<_Tp, _Dp>::unique_ptr(const std::unique_ptr<_Tp, _Dp>&) [with _Tp = moveit::python::Foo; _Dp = std::default_delete<moveit::python::Foo>]’  
`

---

## Comment 1

> Username: rhaschke  
> Created at: 2018-05-27 15:35:08 UTC  
> Url: https://github.com/boostorg/python/issues/202#issuecomment-392340116  

A workaround is to use `std::auto_ptr<Foo>` as the holder and provide a custom wrapper function for any consumer expecting a `unique_ptr<Foo>`:   
  
```cpp  
#include <boost/python.hpp>  
#include <memory>  
  
struct Foo {};  
void Bar_consume(Bar& self, std::auto_ptr<Foo> foo) {  
    return self.consume(std::unique_ptr<Foo>{foo.release()});  
}  
  
BOOST_PYTHON_MODULE(foo) {  
    boost::python::class_<Foo, std::auto_ptr<Foo>, boost::noncopyable>("Foo", boost::python::no_init);  
    boost::python::class_<Bar, boost::noncopyable>("Bar", boost::python::no_init)  
        .def("consume", &Bar_consume);  
}  
```  
However, it would be nicer if `std::unique_ptr` could be used directly as holder.

---

## Comment 2

> Username: o01eg  
> Created at: 2021-03-05 21:47:50 UTC  
> Url: https://github.com/boostorg/python/issues/202#issuecomment-791731634  

`std::auto_ptr` was removed in c++17 so it no longer available workaround.

---

## Comment 3

> Username: o01eg  
> Created at: 2021-03-06 05:37:01 UTC  
> Url: https://github.com/boostorg/python/issues/202#issuecomment-791877835  

In boost 1.74 I got next error when trying to use std::unique_ptr:  
  
```  
In file included from /usr/include/boost/python/to_python_converter.hpp:11,  
                 from /usr/include/boost/python/object/class_wrapper.hpp:8,  
                 from /usr/include/boost/python/object/class_metadata.hpp:12,  
                 from /usr/include/boost/python/class.hpp:23,  
                 from /usr/include/boost/python.hpp:18,  
                 from /mnt/another/srcs/GIT/freeorion/parse/../util/PythonCommon.h:4,  
                 from /mnt/another/srcs/GIT/freeorion/parse/PythonParser.cpp:7:  
/usr/include/boost/python/converter/as_to_python_function.hpp: In instantiation of ‘static PyObject* boost::python::converter::as_to_python_function<T, ToPython>::convert(const void*) [with T = std::unique_ptr<value_ref_wrapper_int>; ToPython = boost::python::objects::class_value_wrapper<std::unique_ptr<value_ref_wrapper_int>, boost::python::objects::make_ptr_instance<value_ref_wrapper_int, boost::python::objects::pointer_holder<std::unique_ptr<value_ref_wrapper_int>, value_ref_wrapper_int> > >; PyObject = _object]’:  
/usr/include/boost/python/to_python_converter.hpp:83:9:   required from ‘boost::python::to_python_converter<T, Conversion, has_get_pytype>::to_python_converter() [with T = std::unique_ptr<value_ref_wrapper_int>; Conversion = boost::python::objects::class_value_wrapper<std::unique_ptr<value_ref_wrapper_int>, boost::python::objects::make_ptr_instance<value_ref_wrapper_int, boost::python::objects::pointer_holder<std::unique_ptr<value_ref_wrapper_int>, value_ref_wrapper_int> > >; bool has_get_pytype = true]’  
/usr/include/boost/python/object/class_wrapper.hpp:37:8:   required from ‘static void boost::python::objects::class_metadata<T, X1, X2, X3>::maybe_register_pointer_to_python(T2*, mpl_::false_*, mpl_::false_*) [with T2 = value_ref_wrapper_int; T = value_ref_wrapper_int; X1 = boost::python::bases<>; X2 = std::unique_ptr<value_ref_wrapper_int>; X3 = boost::noncopyable_::noncopyable; mpl_::false_ = mpl_::bool_<false>]’  
/usr/include/boost/python/object/class_metadata.hpp:229:57:   required from ‘static void boost::python::objects::class_metadata<T, X1, X2, X3>::register_aux2(T2*, Callback) [with T2 = value_ref_wrapper_int; Callback = boost::integral_constant<bool, false>; T = value_ref_wrapper_int; X1 = boost::python::bases<>; X2 = std::unique_ptr<value_ref_wrapper_int>; X3 = boost::noncopyable_::noncopyable]’  
/usr/include/boost/python/object/class_metadata.hpp:218:38:   required from ‘static void boost::python::objects::class_metadata<T, X1, X2, X3>::register_aux(void*) [with T = value_ref_wrapper_int; X1 = boost::python::bases<>; X2 = std::unique_ptr<value_ref_wrapper_int>; X3 = boost::noncopyable_::noncopyable]’  
/usr/include/boost/python/object/class_metadata.hpp:204:37:   required from ‘static void boost::python::objects::class_metadata<T, X1, X2, X3>::register_() [with T = value_ref_wrapper_int; X1 = boost::python::bases<>; X2 = std::unique_ptr<value_ref_wrapper_int>; X3 = boost::noncopyable_::noncopyable]’  
/usr/include/boost/python/class.hpp:460:28:   required from ‘void boost::python::class_<T, X1, X2, X3>::initialize(boost::python::no_init_t) [with W = value_ref_wrapper_int; X1 = boost::python::bases<>; X2 = std::unique_ptr<value_ref_wrapper_int>; X3 = boost::noncopyable_::noncopyable]’  
/usr/include/boost/python/class.hpp:591:5:   required from ‘boost::python::class_<T, X1, X2, X3>::class_(const char*, boost::python::no_init_t) [with W = value_ref_wrapper_int; X1 = boost::python::bases<>; X2 = std::unique_ptr<value_ref_wrapper_int>; X3 = boost::noncopyable_::noncopyable]’  
/mnt/another/srcs/GIT/freeorion/parse/PythonParser.cpp:76:142:   required from here  
/usr/include/boost/python/converter/as_to_python_function.hpp:40:33: error: use of deleted function ‘std::unique_ptr<_Tp, _Dp>::unique_ptr(const std::unique_ptr<_Tp, _Dp>&) [with _Tp = value_ref_wrapper_int; _Dp = std::default_delete<value_ref_wrapper_int>]’  
   40 |         return ToPython::convert(*const_cast<T*>(static_cast<T const*>(x)));  
      |                ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/lib/gcc/x86_64-pc-linux-gnu/9.3.0/include/g++-v9/memory:80,  
                 from /usr/include/boost/system/detail/std_interoperability.hpp:12,  
                 from /usr/include/boost/system/error_code.hpp:963,  
                 from /usr/include/boost/filesystem/path_traits.hpp:22,  
                 from /usr/include/boost/filesystem/path.hpp:26,  
                 from /mnt/another/srcs/GIT/freeorion/parse/Parse.h:10,  
                 from /mnt/another/srcs/GIT/freeorion/parse/PythonParser.h:4,  
                 from /mnt/another/srcs/GIT/freeorion/parse/PythonParser.cpp:1:  
/usr/lib/gcc/x86_64-pc-linux-gnu/9.3.0/include/g++-v9/bits/unique_ptr.h:414:7: note: declared here  
  414 |       unique_ptr(const unique_ptr&) = delete;  
      |       ^~~~~~~~~~  
In file included from /usr/include/boost/python/object/class_metadata.hpp:12,  
                 from /usr/include/boost/python/class.hpp:23,  
                 from /usr/include/boost/python.hpp:18,  
                 from /mnt/another/srcs/GIT/freeorion/parse/../util/PythonCommon.h:4,  
                 from /mnt/another/srcs/GIT/freeorion/parse/PythonParser.cpp:7:  
/usr/include/boost/python/object/class_wrapper.hpp:40:34: note:   initializing argument 1 of ‘static PyObject* boost::python::objects::class_value_wrapper<Src, MakeInstance>::convert(Src) [with Src = std::unique_ptr<value_ref_wrapper_int>; MakeInstance = boost::python::objects::make_ptr_instance<value_ref_wrapper_int, boost::python::objects::pointer_holder<std::unique_ptr<value_ref_wrapper_int>, value_ref_wrapper_int> >; PyObject = _object]’  
   40 |     static PyObject* convert(Src x)  
      |                              ~~~~^  
make[2]: *** [CMakeFiles/freeorionparseobj.dir/build.make:732: CMakeFiles/freeorionparseobj.dir/parse/PythonParser.cpp.o] Error 1  
make[1]: *** [CMakeFiles/Makefile2:388: CMakeFiles/freeorionparseobj.dir/all] Error 2  
make: *** [Makefile:182: all] Error 2  
```

---

## Comment 4

> Username: rhaschke  
> Created at: 2021-03-06 10:49:25 UTC  
> Url: https://github.com/boostorg/python/issues/202#issuecomment-791911071  

boost::python isn't maintained anymore. I suggest switching to [pybind11](https://github.com/pybind/pybind11).

---

## Comment 5

> Username: o01eg  
> Created at: 2021-03-06 12:58:38 UTC  
> Url: https://github.com/boostorg/python/issues/202#issuecomment-791933905  

It looks like pybind11 have issues with it too: https://github.com/pybind/pybind11/issues/1132

---

## Comment 6

> Username: MasterBe  
> Created at: 2024-08-29 22:37:42 UTC  
> Url: https://github.com/boostorg/python/issues/202#issuecomment-2319182266  

> boost::python isn't maintained anymore. I suggest switching to [pybind11](https://github.com/pybind/pybind11).  
  
Is this true? If not please don't make such statement you are misleading people using boost python.

---

## Comment 7

> Username: CEXT-Dan  
> Created at: 2024-08-29 23:56:07 UTC  
> Url: https://github.com/boostorg/python/issues/202#issuecomment-2319488950  

It’s an old comment.   
  
True, the maintenance activity is probably slower than pybind11.  
  
I did the research before starting on Python for AutoCAD wrappers https://github.com/CEXT-Dan/PyRx  
boost::python has a much better performance profile than pybind11, like 4x, which matters most for my project  
this may be of interest https://youtu.be/rB7c69Z5Kus?t=710  
  
with regards to the original issue, use a std::shared_ptr  
std::unique_ptr won’t work because the current py-object containers don’t have move operations.

---

## Comment 8

> Username: rhaschke  
> Created at: 2024-08-30 00:30:22 UTC  
> Url: https://github.com/boostorg/python/issues/202#issuecomment-2319545999  

You are right, it is still maintained, but development has ceased: modern features like rvalue refs as arguments are and probably will never be supported. pybind11 supports `std::unique_ptr` in their `smart_holder` branch nowadays.

---

## Comment 9

> Username: MasterBe  
> Created at: 2024-08-30 00:41:55 UTC  
> Url: https://github.com/boostorg/python/issues/202#issuecomment-2319564113  

@rhaschke previous comment confirms boost python is 4x faster. Will unique pointer make pybind 4+epsilon x faster than boost python?

---

## Comment 10

> Username: CEXT-Dan  
> Created at: 2024-08-30 01:29:47 UTC  
> Updated at: 2024-08-30 01:30:34 UTC  
> Url: https://github.com/boostorg/python/issues/202#issuecomment-2319632069  

From a python perspective, there would be no measurable difference in performance with the regards of which pointer is used.

---

## Comment 11

> Username: rhaschke  
> Created at: 2024-08-30 06:55:54 UTC  
> Url: https://github.com/boostorg/python/issues/202#issuecomment-2320262425  

Don't overestimate the performance benefit of boost::python. This was a micro benchmark, essentially testing the function call overhead (in the range of ns). If you call reasonable functions (in the range of ms), this overhead is negligible for any framework.

---

## Comment 12

> Username: CEXT-Dan  
> Created at: 2024-08-30 08:01:29 UTC  
> Url: https://github.com/boostorg/python/issues/202#issuecomment-2320413150  

> Don't overestimate the performance benefit of boost::python. This was a micro benchmark, essentially testing the function call overhead (in the range of ns). If you call reasonable functions (in the range of ms), this overhead is negligible for any framework.  
  
I agree this is the case for most projects.  
  
function call overhead is a huge deal in a CAD environment, i.e. if you add all the calls it takes to iterate over a large collection of wrapped objects. You have the cost of construction, advancing the iterator, the call into the object, then the destructor. That really starts to add up  
  
I’m not saying boost is all unicorn rainbow sparkles, it seemed to be the best for my project, at the time I needed to make that choice.
