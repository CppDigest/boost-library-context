# #285 - boost::python::numpy causing python crash when numpy not installed [Open]

> Username: brian-pantano  
> Created at: 2019-12-19 20:36:43 UTC  
> Updated at: 2019-12-19 20:40:04 UTC  
> Url: https://github.com/boostorg/python/issues/285  

The following code causes python to exit (crash?) upon import when numpy is not installed. It looks like a ModuleNotFoundError does get thrown but it's not catchable and python simply exits. It's hard to tell if this is exit() being called or an actual crash. I would expect a catchable ModuleNotFoundError  or ImportError to be thrown and python not to exit.  
  
	#define BOOST_PYTHON_STATIC_LIB  
	#define BOOST_NUMPY_STATIC_LIB  
	#include <boost/python.hpp>  
  
	#include <boost/python.hpp>  
	#include <boost/python/suite/indexing/vector_indexing_suite.hpp>  
	#include <boost/python/numpy.hpp>  
  
	namespace py = boost::python;  
	namespace np = boost::python::numpy;  
  
	template<class T>  
	np::ndarray to_numpy(const std::vector<T> &vec)  
	{  
		Py_intptr_t shape[1] = { static_cast<Py_intptr_t>(vec.size()) };  
		np::ndarray result = np::zeros(1, shape, np::dtype::get_builtin<T>());  
		std::copy(vec.begin(), vec.end(), reinterpret_cast<T*>(result.get_data()));  
		return result;  
	}  
  
	BOOST_PYTHON_MODULE(HelloBoostPython)  
	{  
		np::initialize();  
  
		py::class_<std::vector<double>>("DoubleVector")  
			.def(py::init<size_t>())  
			.def(py::init<size_t, const double&>())  
			.def(py::vector_indexing_suite<std::vector<double>>())  
			.def("to_numpy", to_numpy<double>)  
			;  
	}  
  
prompt:  
  
	(base) C:\Development\HelloBoostPython\Debug>python  
	Python 3.7.4 (default, Aug  9 2019, 18:22:51) [MSC v.1915 32 bit (Intel)] :: Anaconda, Inc. on win32  
	Type "help", "copyright", "credits" or "license" for more information.  
	>>> import HelloBoostPython  
	ModuleNotFoundError: No module named 'numpy'  
	ImportError: numpy.core._multiarray_umath failed to import  
  
	(base) C:\Development\HelloBoostPython\Debug>  
  
If it matters, this is with boost 1.70 using visual studio 2017 to build.
