# #118 - scons installation broken [Closed]

> Username: BoltzmannBrain  
> Created at: 2017-03-05 19:01:51 UTC  
> Updated at: 2018-03-07 21:58:33 UTC  
> Closed at: 2018-03-07 21:58:33 UTC  
> Url: https://github.com/boostorg/python/issues/118  

Following the setup instructions in the README, running `scons` (with and without `config` flags) results in   
```  
scons: Reading SConscript files ...  
scons: *** (<class 'sphinx4scons.SphinxBuilderNotFound'>, 'Could not detect sphinx-build script')  Stop.  
```  
  
Alternatively, do I need to build with scons? I've installed Boost 1.63.0 via homebrew, but trying to `make` something like   
```  
#include <boost/python.hpp>  
#include <boost/python/numpy.hpp>  
  
namespace bpy = boost::python;  
namespace bnp = boost::python::numpy;  
  
...  
```  
results in   
```  
Undefined symbols for architecture x86_64:  
  "boost::python::converter::object_manager_traits<boost::python::numpy::ndarray>::get_pytype()", referenced from:  
      boost::python::detail::caller_arity<1u>::impl<OutMessages (*)(boost::python::numpy::ndarray const&), boost::python::default_call_policies, boost::mpl::vector2<OutMessages, boost::python::numpy::ndarray const&> >::operator()(_object*, _object*) in hello_boost.o  
ld: symbol(s) not found for architecture x86_64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
make: *** [hello_boost.so] Error 1  
```  
Note `make` was successful if I don't use the `bpy` and `bnp` types, so my Makefile is correct.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-03-05 21:11:16 UTC  
> Url: https://github.com/boostorg/python/issues/118#issuecomment-284263022  

To use scons you also need sphinx (as the error indicates). As to your 'make' error: I'd need to see the command line (plus full error) to be able to help.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2017-03-05 21:28:52 UTC  
> Url: https://github.com/boostorg/python/issues/118#issuecomment-284264292  

In fact, I suspect you are linking with '-lboost_python' but not '-lboost_numpy'. Just guessing...

---

## Comment 3

> Username: BoltzmannBrain  
> Created at: 2017-03-05 23:15:55 UTC  
> Url: https://github.com/boostorg/python/issues/118#issuecomment-284271767  

Thank you for the replies @stefanseefeld. I've made sure I have sphinx installed, and the same error persists. Regarding the make error, you were correct that I need to include `-lboost_numpy`, thanks!

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2017-03-05 23:25:49 UTC  
> Url: https://github.com/boostorg/python/issues/118#issuecomment-284272466  

Ok, good to know. As to the scons error: for some reason scons can't find the scons-build executable.

---

## Comment 5

> Username: garyfurnish  
> Created at: 2017-08-21 19:23:44 UTC  
> Url: https://github.com/boostorg/python/issues/118#issuecomment-323829709  

I have seen this error on scons if I install scons using pip then install sphinx.  The fix was to reinstall scons after installing sphinx.  I am not sure if this is the problem here.

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2018-03-07 21:58:33 UTC  
> Url: https://github.com/boostorg/python/issues/118#issuecomment-371299686  

I'm no longer maintaining the scons-based build system, in favour of a new one based on https://stefanseefeld.github.io/faber.
