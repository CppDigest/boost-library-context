# #18 Restructure code [Merged]

> Username: CromwellEnage  
> Created at: 2018-10-24 17:02:12 UTC  
> Updated at: 2018-10-25 09:56:20 UTC  
> Merged at: 2018-10-25 01:53:22 UTC  
> Closed at: 2018-10-25 01:53:22 UTC  
> Url: https://github.com/boostorg/parameter/pull/18  

<boost/parameter/aux_/arg_list.hpp>:  
* Move forward declarations to their own header files.  
  
<boost/parameter/aux_/tagged_argument.hpp>:  
* Move boost::parameter::aux::tagged_argument_base, boost::parameter::aux::is_tagged_argument_aux, and boost::parameter::aux::is_tagged_argument to <boost/parameter/aux_/is_tagged_argument.hpp>  
* Remove unnecessary forward declarations.  
  
<boost/parameter/name.hpp>:  
* Move support metafunction definitions and BOOST_PARAMETER_TAG_PLACEHOLDER_TYPE macro definition to <boost/parameter/aux_/name.hpp>.  
* Move BOOST_PARAMETER_IS_BINARY macro definition to <boost/parameter/aux_/preprocessor/is_binary.hpp>.  
* Use BOOST_PARAMETER_TAG_PLACEHOLDER_TYPE macro.  
  
<boost/parameter/aux_/overloads.hpp> currently works at the preprocessor level, so move to <boost/parameter/aux_/preprocessor/overloads.hpp>.  
  
<boost/parameter/parameters.hpp>:  
* Move boost::parameter::aux::use_default to <boost/parameter/aux_/use_default.hpp>.  
* Move boost::parameter::required, boost::parameter::optional, and boost::parameter::deduced metafunction definitions to their own header files in directory boost/parameter.  
* Move other support metafunction definitions to their own header files in directory boost/parameter/aux_/pack.  
* Move support macro definitions to <boost/parameter/aux_/preprocessor/no_perfect_forwarding_begin.hpp> and their corresponding #undef statements to <boost/parameter/aux_/preprocessor/no_perfect_forwarding_end.hpp>.  
  
<boost/parameter/preprocessor.hpp>:  
* Move voidstar type definition to <boost/parameter/aux_/void.hpp>.  
* Move support metafunctions to their own header files in directory boost/parameter/aux_/pp_impl.  
* Move BOOST_PARAMETER_IS_NULLARY macro definition to <boost/parameter/aux_/preprocessor/is_nullary.hpp>.  
* Move other support macros to header files in directory boost/parameter/aux_/preprocessor/impl.  
  
<boost/parameter/aux_/cast.hpp>  
* Move boost::parameter::aux::use_default_tag type definition to <boost/parameter/aux_/use_default_tag.hpp>.  
* Move BOOST_PARAMETER_FUNCTION_CAST macro definition and its supporting contents to <boost/parameter/aux_/preprocessor/impl/function_cast.hpp> but leave this header as a forwarding header.  
  
<boost/parameter/aux_/parenthesized_type.hpp> currently defines the support macro BOOST_PARAMETER_PARENTHESIZED_TYPE for <boost/parameter/preprocessor.hpp>, so move its contents to <boost/parameter/aux_/preprocessor/impl/parenthesized_type.hpp> but leave this header as a forwarding header.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-10-24 19:06:29 UTC  
> Url: https://github.com/boostorg/parameter/pull/18#issuecomment-432789415  

Some of these constructs which are part of the public interface, such as boost::parameter::required, boost::parameter::optional, and boost::parameter::deduced, are documented as being in boost/parameter/parameters.hpp. If they are moved tot their own header files do we not need to document this ? We do not have to document anything which is not part of the public interface, so I am not worried about all the support constructs which are internal to Parameter.

---

## Comment 2

> Username: CromwellEnage  
> Created_at: 2018-10-24 21:05:45 UTC  
> Url: https://github.com/boostorg/parameter/pull/18#issuecomment-432828058  

I've updated the reference documentation to reflect the structural changes.  The additional statements that boost/parameter/parameters.hpp includes these header files (not directly, but by including other header files that in turn include them) should serve as a reassurance that the changes won't break existing code.

---

## Comment 3

> Username: eldiener  
> Created_at: 2018-10-24 21:06:22 UTC  
> Url: https://github.com/boostorg/parameter/pull/18#issuecomment-432828260  

Looks good. I am waiting for the CI tests to finish and then will Merge as long as no new problems show up there.

---
