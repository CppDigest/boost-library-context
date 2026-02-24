# #281 - List of non BOOST-prefixed macros [Open]

> Username: ned14  
> Created at: 2019-12-16 13:57:53 UTC  
> Updated at: 2019-12-16 13:57:53 UTC  
> Url: https://github.com/boostorg/python/issues/281  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./boost/python/return_opaque_pointer.hpp:# ifndef RETURN_OPAQUE_POINTER_HPP_  
./boost/python/return_opaque_pointer.hpp:# define RETURN_OPAQUE_POINTER_HPP_  
./boost/python/return_opaque_pointer.hpp:# endif // RETURN_OPAQUE_POINTER_HPP_  
./boost/python/register_ptr_to_python.hpp:#ifndef REGISTER_PTR_TO_PYTHON_HPP  
./boost/python/register_ptr_to_python.hpp:#define REGISTER_PTR_TO_PYTHON_HPP  
./boost/python/register_ptr_to_python.hpp:#endif // REGISTER_PTR_TO_PYTHON_HPP  
./libs/python/test/complicated.hpp:#ifndef COMPLICATED_DWA20011215_HPP  
./libs/python/test/complicated.hpp:# define COMPLICATED_DWA20011215_HPP  
./libs/python/test/complicated.hpp:#endif // COMPLICATED_DWA20011215_HPP  
./boost/python/signature.hpp:# ifndef SIGNATURE_JDG20020813_HPP  
./boost/python/signature.hpp:#  define SIGNATURE_JDG20020813_HPP  
./boost/python/signature.hpp:# endif // SIGNATURE_JDG20020813_HPP  
./boost/python/object/class.hpp:#ifndef CLASS_DWA20011214_HPP  
./boost/python/object/class.hpp:# define CLASS_DWA20011214_HPP  
./boost/python/object/class.hpp:#endif // CLASS_DWA20011214_HPP  
./boost/python/object/class_wrapper.hpp:#ifndef CLASS_WRAPPER_DWA20011221_HPP  
./boost/python/object/class_wrapper.hpp:# define CLASS_WRAPPER_DWA20011221_HPP  
./boost/python/object/class_wrapper.hpp:#endif // CLASS_WRAPPER_DWA20011221_HPP  
./boost/python/object/function.hpp:#ifndef FUNCTION_DWA20011214_HPP  
./boost/python/object/function.hpp:# define FUNCTION_DWA20011214_HPP  
./boost/python/object/function.hpp:#endif // FUNCTION_DWA20011214_HPP  
./boost/python/object/stl_iterator_core.hpp:#ifndef STL_ITERATOR_CORE_EAN20051028_HPP  
./boost/python/object/stl_iterator_core.hpp:# define STL_ITERATOR_CORE_EAN20051028_HPP  
./boost/python/object/stl_iterator_core.hpp:#endif // STL_ITERATOR_CORE_EAN20051028_HPP  
./boost/python/object/function_doc_signature.hpp:#ifndef FUNCTION_SIGNATURE_20070531_HPP  
./boost/python/object/function_doc_signature.hpp:# define FUNCTION_SIGNATURE_20070531_HPP  
./boost/python/object/function_doc_signature.hpp:#endif //FUNCTION_SIGNATURE_20070531_HPP  
./boost/python/object/value_holder.hpp:# ifndef VALUE_HOLDER_DWA20011215_HPP  
./boost/python/object/value_holder.hpp:#  define VALUE_HOLDER_DWA20011215_HPP  
./boost/python/object/value_holder.hpp:# endif // VALUE_HOLDER_DWA20011215_HPP  
./boost/python/object/pickle_support.hpp:#ifndef BOOST_PYTHON_OBJECT_PICKLE_SUPPORT_RWGK20020603_HPP  
./boost/python/object/pickle_support.hpp:# define BOOST_PYTHON_OBJECT_PICKLE_SUPPORT_RWGK20020603_HPP  
./boost/python/object/pickle_support.hpp:#endif // BOOST_PYTHON_OBJECT_PICKLE_SUPPORT_RWGK20020603_HPP  
./boost/python/object/forward.hpp:#ifndef FORWARD_DWA20011215_HPP  
./boost/python/object/forward.hpp:# define FORWARD_DWA20011215_HPP  
./boost/python/object/forward.hpp:#endif // FORWARD_DWA20011215_HPP  
./boost/python/object/make_holder.hpp:# ifndef MAKE_HOLDER_DWA20011215_HPP  
./boost/python/object/make_holder.hpp:#  define MAKE_HOLDER_DWA20011215_HPP  
./boost/python/object/make_holder.hpp:# endif // MAKE_HOLDER_DWA20011215_HPP  
./boost/python/object/pointer_holder.hpp:# ifndef POINTER_HOLDER_DWA20011215_HPP  
./boost/python/object/pointer_holder.hpp:#  define POINTER_HOLDER_DWA20011215_HPP  
./boost/python/object/pointer_holder.hpp:# endif // POINTER_HOLDER_DWA20011215_HPP  
./boost/python/str.hpp:#ifndef STR_20020703_HPP  
./boost/python/str.hpp:#define STR_20020703_HPP  
./boost/python/str.hpp:#endif // STR_20020703_HPP  
./boost/python/make_function.hpp:#ifndef MAKE_FUNCTION_DWA20011221_HPP  
./boost/python/make_function.hpp:# define MAKE_FUNCTION_DWA20011221_HPP  
./boost/python/make_function.hpp:#endif // MAKE_FUNCTION_DWA20011221_HPP  
./boost/python/slice.hpp:#ifndef BOOST_PYTHON_SLICE_JDB20040105_HPP  
./boost/python/slice.hpp:#define BOOST_PYTHON_SLICE_JDB20040105_HPP  
./boost/python/slice.hpp:#endif // !defined BOOST_PYTHON_SLICE_JDB20040105_HPP  
./boost/python/exec.hpp:#ifndef EXEC_SS20050616_HPP  
./boost/python/exec.hpp:# define EXEC_SS20050616_HPP  
./boost/python/numpy/config.hpp:#ifndef CONFIG_NUMPY20170215_H_  
./boost/python/numpy/config.hpp:# define CONFIG_NUMPY20170215_H_  
./boost/python/numpy/config.hpp:#endif // CONFIG_NUMPY20170215_H_  
./boost/python/detail/borrowed_ptr.hpp:#ifndef BORROWED_PTR_DWA20020601_HPP  
./boost/python/detail/borrowed_ptr.hpp:# define BORROWED_PTR_DWA20020601_HPP  
./boost/python/detail/borrowed_ptr.hpp:#endif // #ifndef BORROWED_PTR_DWA20020601_HPP  
./boost/python/detail/signature.hpp:# ifndef SIGNATURE_DWA20021121_HPP  
./boost/python/detail/signature.hpp:#  define SIGNATURE_DWA20021121_HPP  
./boost/python/detail/signature.hpp:# endif // SIGNATURE_DWA20021121_HPP  
./boost/python/detail/caller.hpp:# ifndef CALLER_DWA20021121_HPP  
./boost/python/detail/caller.hpp:#  define CALLER_DWA20021121_HPP  
./boost/python/detail/caller.hpp:# endif // CALLER_DWA20021121_HPP  
./boost/python/detail/decref_guard.hpp:#ifndef DECREF_GUARD_DWA20021220_HPP  
./boost/python/detail/decref_guard.hpp:# define DECREF_GUARD_DWA20021220_HPP  
./boost/python/detail/decref_guard.hpp:#endif // DECREF_GUARD_DWA20021220_HPP  
./boost/python/detail/defaults_def.hpp:#ifndef DEFAULTS_DEF_JDG20020811_HPP  
./boost/python/detail/defaults_def.hpp:#define DEFAULTS_DEF_JDG20020811_HPP  
./boost/python/detail/defaults_def.hpp:#endif // DEFAULTS_DEF_JDG20020811_HPP  
./boost/python/detail/defaults_gen.hpp:#ifndef DEFAULTS_GEN_JDG20020807_HPP  
./boost/python/detail/defaults_gen.hpp:#define DEFAULTS_GEN_JDG20020807_HPP  
./boost/python/detail/defaults_gen.hpp:#endif // DEFAULTS_GEN_JDG20020807_HPP  
./boost/python/detail/translate_exception.hpp:#ifndef TRANSLATE_EXCEPTION_TDS20091020_HPP  
./boost/python/detail/translate_exception.hpp:# define TRANSLATE_EXCEPTION_TDS20091020_HPP  
./boost/python/detail/invoke.hpp:# ifndef INVOKE_DWA20021122_HPP  
./boost/python/detail/invoke.hpp:#  define INVOKE_DWA20021122_HPP  
./boost/python/detail/invoke.hpp:# endif // INVOKE_DWA20021122_HPP  
./boost/python/make_constructor.hpp:#ifndef MAKE_CONSTRUCTOR_DWA20011221_HPP  
./boost/python/make_constructor.hpp:# define MAKE_CONSTRUCTOR_DWA20011221_HPP  
./boost/python/make_constructor.hpp:#endif // MAKE_CONSTRUCTOR_DWA20011221_HPP  
./boost/python/converter/pytype_function.hpp:#ifndef WRAP_PYTYPE_NM20070606_HPP  
./boost/python/converter/pytype_function.hpp:# define WRAP_PYTYPE_NM20070606_HPP  
./boost/python/converter/pytype_function.hpp:#endif // WRAP_PYTYPE_NM20070606_HPP  
./boost/python/converter/registry.hpp:#ifndef REGISTRY_DWA20011127_HPP  
./boost/python/converter/registry.hpp:# define REGISTRY_DWA20011127_HPP  
./boost/python/converter/registry.hpp:#endif // REGISTRY_DWA20011127_HPP  
./boost/python/ssize_t.hpp:#ifndef BOOST_PYTHON_SSIZE_T_RWGK20060924_HPP  
./boost/python/ssize_t.hpp:# define BOOST_PYTHON_SSIZE_T_RWGK20060924_HPP  
./boost/python/ssize_t.hpp:#endif // BOOST_PYTHON_SSIZE_T_RWGK20060924_HPP  
./boost/python/tuple.hpp:#ifndef TUPLE_20020706_HPP  
./boost/python/tuple.hpp:#define TUPLE_20020706_HPP  
./boost/python/return_by_value.hpp:#ifndef BY_VALUE_DWA20021015_HPP  
./boost/python/return_by_value.hpp:# define BY_VALUE_DWA20021015_HPP  
./boost/python/return_by_value.hpp:#endif // BY_VALUE_DWA20021015_HPP  
./boost/python/module_init.hpp:#ifndef MODULE_INIT_DWA20020722_HPP  
./boost/python/module_init.hpp:# define MODULE_INIT_DWA20020722_HPP  
./boost/python/module_init.hpp:#endif // MODULE_INIT_DWA20020722_HPP  
./boost/python/init.hpp:#ifndef INIT_JDG20020820_HPP  
./boost/python/init.hpp:#define INIT_JDG20020820_HPP  
./boost/python/init.hpp:#endif // INIT_JDG20020820_HPP  
./boost/python/docstring_options.hpp:#ifndef DOCSTRING_OPTIONS_RWGK20060111_HPP  
./boost/python/docstring_options.hpp:# define DOCSTRING_OPTIONS_RWGK20060111_HPP  
./boost/python/docstring_options.hpp:#endif // DOCSTRING_OPTIONS_RWGK20060111_HPP  
./boost/python/dict.hpp:#ifndef DICT_20020706_HPP  
./boost/python/dict.hpp:#define DICT_20020706_HPP  
./boost/python/import.hpp:#ifndef IMPORT_SS20050624_HPP  
./boost/python/import.hpp:# define IMPORT_SS20050624_HPP  
./boost/python/stl_iterator.hpp:#ifndef STL_ITERATOR_EAN20051028_HPP  
./boost/python/stl_iterator.hpp:# define STL_ITERATOR_EAN20051028_HPP  
./boost/python/stl_iterator.hpp:#endif // STL_ITERATOR_EAN20051028_HPP  
./boost/python/module.hpp:#endif // MODULE_DWA20011221_HPP  
```
