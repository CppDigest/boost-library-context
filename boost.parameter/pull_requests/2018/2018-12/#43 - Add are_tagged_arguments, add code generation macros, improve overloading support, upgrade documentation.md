# #43  Add are_tagged_arguments, add code generation macros, improve overloading support, upgrade documentation [Closed]

> Username: CromwellEnage  
> Created at: 2018-12-22 10:27:50 UTC  
> Updated at: 2019-01-02 04:13:00 UTC  
> Closed at: 2019-01-02 01:32:10 UTC  
> Url: https://github.com/boostorg/parameter/pull/43  

* Add variadic metafunction boost::parameter::are_tagged_arguments to help improve overload resolution capabilities. Used by new BOOST_PARAMETER_NO_SPEC_* code generation macros.  
* Add code generation macros BOOST_PARAMETER_BASIC_FUNCTION_CALL_OPERATOR, BOOST_PARAMETER_BASIC_CONST_FUNCTION_CALL_OPERATOR, BOOST_PARAMETER_NO_SPEC_FUNCTION, BOOST_PARAMETER_NO_SPEC_MEMBER_FUNCTION, BOOST_PARAMETER_NO_SPEC_CONST_MEMBER_FUNCTION, BOOST_PARAMETER_NO_SPEC_FUNCTION_CALL_OPERATOR, BOOST_PARAMETER_NO_SPEC_CONST_FUNCTION_CALL_OPERATOR, BOOST_PARAMETER_NO_SPEC_CONSTRUCTOR, and BOOST_PARAMETER_NO_SPEC_NO_BASE_CONSTRUCTOR.  
* Enable function call operator overloads & overloads of the same name to coexist even with deduced arguments.  
* Add usage examples to & remove unnecessary implementation details from reference documentation.

---
