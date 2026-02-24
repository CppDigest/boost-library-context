# #112 - std::vector<std::tuple<>> working only with NoProxy set with vector_indexing_suite [Open]

> Username: Svalorzen  
> Created at: 2017-02-17 09:25:11 UTC  
> Updated at: 2017-02-17 09:25:11 UTC  
> Url: https://github.com/boostorg/python/issues/112  

Hi, I'm trying to export from C++ an std::vector of a tuple. Unfortunately, while both the vector (exported via `vector_indexing_suite`) and the tuple (exported via `to_python_converter`) themselves are exported correctly and can be accessed, accessing a tuple element *from* the vector results in an `No Python class registered for C++ class` error. This does not happen when the `vector_indexing_suite` has the `NoProxy` template parameter set to `true`, which for me is undesirable.  
  
Am I doing something wrong, or is there a way to bypass this limitation?  
  
Here is a MVE:  
  
```  
#include <tuple>  
#include <vector>  
  
#include <boost/python.hpp>  
#include <boost/python/suite/indexing/vector_indexing_suite.hpp>  
  
template <typename T>  
struct TupleToPython {  
    TupleToPython() {  
        boost::python::to_python_converter<T, TupleToPython<T>>();  
    }  
  
    template<int...>  
    struct sequence {};  
  
    template<int N, int... S>  
    struct generator : generator<N-1, N-1, S...> { };  
  
    template<int... S>  
    struct generator<0, S...> {  
        using type = sequence<S...>;  
    };  
  
    template <int... I>  
    static boost::python::tuple boostConvertImpl(const T& t, sequence<I...>) {  
        return boost::python::make_tuple(std::get<I>(t)...);  
    }  
  
    template <typename... Args>  
    static boost::python::tuple boostConvert(const std::tuple<Args...> & t) {  
        return boostConvertImpl(t, typename generator<sizeof...(Args)>::type());  
    }  
  
    static PyObject* convert(const T& t) {  
        return boost::python::incref(boostConvert(t).ptr());  
    }  
};  
  
using MyTuple = std::tuple<int>;  
using Tuples = std::vector<MyTuple>;  
  
MyTuple makeMyTuple() {  
    return MyTuple();  
}  
  
Tuples makeTuples() {  
    return Tuples{MyTuple()};  
}  
  
BOOST_PYTHON_MODULE(h)  
{  
    using namespace boost::python;  
  
    TupleToPython<MyTuple>();  
    def("makeMyTuple", makeMyTuple);  
  
    class_<std::vector<MyTuple>>{"Tuples"}  
        .def(vector_indexing_suite<std::vector<MyTuple>>());  
    def("makeTuples", makeTuples);  
}  
```  
  
Then from Python:  
  
```  
>>> print makeMyTuple()  
(0,)  
>>> print makeTuples()  
<h.Tuples object at 0x7f82cc291d50>  
>>> print makeTuples()[0]  
Traceback (most recent call last):  
  File "<stdin>", line 1, in <module>  
TypeError: No Python class registered for C++ class std::tuple<int>  
>>>   
```
