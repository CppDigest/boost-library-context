# #51 Add are_tagged_arguments<>, compose(), and code generation macros [Closed]

> Username: CromwellEnage  
> Created at: 2019-01-04 12:16:58 UTC  
> Updated at: 2019-01-04 13:38:04 UTC  
> Closed at: 2019-01-04 13:38:04 UTC  
> Url: https://github.com/boostorg/parameter/pull/51  

* Add variadic metafunction boost::parameter::are_tagged_arguments to help improve overload resolution capabilities.  Used by compose() and the new BOOST_PARAMETER_NO_SPEC_* code generation macros.  
* Add variadic function template compose() which takes in named arguments and returns them in an argument pack.  
* Add code generation macros BOOST_PARAMETER_BASIC_FUNCTION_CALL_OPERATOR, BOOST_PARAMETER_BASIC_CONST_FUNCTION_CALL_OPERATOR, BOOST_PARAMETER_NO_SPEC_FUNCTION, BOOST_PARAMETER_NO_SPEC_MEMBER_FUNCTION, BOOST_PARAMETER_NO_SPEC_CONST_MEMBER_FUNCTION, BOOST_PARAMETER_NO_SPEC_FUNCTION_CALL_OPERATOR, BOOST_PARAMETER_NO_SPEC_CONST_FUNCTION_CALL_OPERATOR, BOOST_PARAMETER_NO_SPEC_CONSTRUCTOR, and BOOST_PARAMETER_NO_SPEC_NO_BASE_CONSTRUCTOR.

---
