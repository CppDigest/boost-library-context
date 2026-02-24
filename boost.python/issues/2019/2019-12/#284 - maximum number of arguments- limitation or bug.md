# #284 - maximum number of arguments: limitation or bug ? [Closed]

> Username: gst  
> Created at: 2019-12-19 17:56:21 UTC  
> Updated at: 2020-01-09 17:58:34 UTC  
> Closed at: 2019-12-19 19:33:16 UTC  
> Url: https://github.com/boostorg/python/issues/284  

Hi,  
  
I would like to ask if it's a bug or an implementation limitation/detail that when I try to expose a function with 15 arguments it works out of the box, but if add one more arg (that is 16), then I get an enormous compilation error output. with head / relevant information being :   
  
```  
[ 95%] Building CXX object src/pythonbinding/CMakeFiles/_axstatistics.dir/pythonbinding.cpp.o  
In file included from /usr/include/boost/python/data_members.hpp:15:0,  
                 from /usr/include/boost/python/class.hpp:17,  
                 from /usr/include/boost/python.hpp:18,  
                 from /home/gregory.starck/projects/statistics/src/pythonbinding/pythonbinding.cpp:6:  
/usr/include/boost/python/make_function.hpp: In instantiation of ‘boost::python::api::object boost::python::make_function(F) [with F = void (*)(int, int, int, int, int, int, int, int, int, int, int, int, int, int, int, int)]’:  
/usr/include/boost/python/def.hpp:82:58:   required from ‘boost::python::api::object boost::python::detail::make_function1(T, ...) [with T = void (*)(int, int, int, int, int, int, int, int, int, int, int, int, int, int, int, int)]’  
/usr/include/boost/python/def.hpp:91:59:   required from ‘void boost::python::def(const char*, Fn) [with Fn = void (*)(int, int, int, int, int, int, int, int, int, int, int, int, int, int, int, int)]’  
/home/gregory.starck/projects/statistics/src/pythonbinding/pythonbinding.cpp:147:40:   required from here  
/usr/include/boost/python/make_function.hpp:104:57: error: no matching function for call to ‘get_signature(void (*&)(int, int, int, int, int, int, int, int, int, int, int, int, int, int, int, int))’  
         f,default_call_policies(), detail::get_signature(f));  
                                    ~~~~~~~~~~~~~~~~~~~~~^~~  
```  
  
Thanks for any confirmation or information ..

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2019-12-19 18:03:38 UTC  
> Url: https://github.com/boostorg/python/issues/284#issuecomment-567598370  

I have to admit that I wasn't aware of that limit, simply because I never had a need to map functions taking that many arguments.  
But let me speculate as to the reason for the behaviour you observe:  
Boost.Python was written well before C++11, and thus, at a time where variadic templates didn't exist. Thus, to allow it to infer function signatures, someone had to explicitly write template specializations with specific arities up to some upper limit - which happens to be 16.  
  
Nowadays it should be possible to replace the corresponding code by a variadic template that recursively unpacks the argument list, so that limit disappears. Feel free to submit a feature request, or better yet, a pull request.

---

## Comment 2

> Username: gst  
> Created at: 2019-12-19 19:06:21 UTC  
> Url: https://github.com/boostorg/python/issues/284#issuecomment-567621139  

Hi, thanks for the confirmation.  
I m pretty sure this is above my current c++ skill unfortunately.  
feel free to close.

---

## Comment 3

> Username: gst  
> Created at: 2020-01-09 17:58:33 UTC  
> Url: https://github.com/boostorg/python/issues/284#issuecomment-572679269  

Hi @stefanseefeld   
  
how do we submit a feature request ? :)   
  
The project I'm working has more than one place (that I could workaround in a clean way) where we have functions with more than 15 arguments that we want to able to be called from Python.  
  
I've just have seen that there is BOOST_PYTHON_MAX_ARITY macro. and apparently we would only need to raise that value to say.. 24 and that would make it ?  
  
Thanks.
