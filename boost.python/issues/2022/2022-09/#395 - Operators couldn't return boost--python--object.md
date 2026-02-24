# #395 - Operators couldn't return boost::python::object [Open]

> Username: o01eg  
> Created at: 2022-09-29 18:45:43 UTC  
> Updated at: 2022-09-29 18:45:43 UTC  
> Url: https://github.com/boostorg/python/issues/395  

When I trying to define operator which returns boost::python::object I get error ` ‘struct boost::python::converter::arg_to_python<boost::python::api::object>’ has no member named ‘release’`  
  
`def` method for operators doesn't accept additional arguments to define other converters.
