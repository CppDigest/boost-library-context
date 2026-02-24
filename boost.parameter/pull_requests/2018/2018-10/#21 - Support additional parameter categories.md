# #21  Support additional parameter categories [Merged]

> Username: CromwellEnage  
> Created at: 2018-10-25 23:22:26 UTC  
> Updated at: 2018-10-26 04:59:11 UTC  
> Merged at: 2018-10-26 01:59:20 UTC  
> Closed at: 2018-10-26 01:59:20 UTC  
> Url: https://github.com/boostorg/parameter/pull/21  

Add parameter category qualifier "forward" (current qualifiers are "in", "out", and "in_out") based on http://www.modernescpp.com/index.php/c-core-guidelines-how-to-pass-function-parameters.  Add new usage syntax BOOST_PARAMETER_NAME((object-name), namespace-name) qualifier(tag-name)) and BOOST_PARAMETER_NAME(qualifier(name)).  (Existing code that uses qualifiers directly and correctly with BOOST_PARAMETER_FUNCTION and other code generation macros should remain unaffected for now, so no breaking changes.)  The reason for the change in usage is to enable applying of parameter category constraints to Boost.Parameter-enabled functions and constructors invoked through argument composition.  (Otherwise, it is currently possible to use argument composition to bypass parameter category constraints applied in BOOST_PARAMETER_FUNCTION et. al.) See "test/compose.cpp" for example usage.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-10-26 02:52:01 UTC  
> Url: https://github.com/boostorg/parameter/pull/21#issuecomment-433269446  

I have been happy to merge your work to 'develop', and will continue to do so upon checking what you have done. But I want to hold back your changes from the next release of Boost, which will be 1.69, which will be closing for changes on this coming Saturday 10/27/18. After all your changes have been in 'develop' when 1.69 come out, we can plan on merging to 'master' for Boost 1.70, and you can explain your changes in the Parameter release notes of Boost 1.70, and also if you like in the developers mailing list. Boost usually cycles to a new release every 3 months or so.

---

## Comment 2

> Username: CromwellEnage  
> Created_at: 2018-10-26 04:59:11 UTC  
> Url: https://github.com/boostorg/parameter/pull/21#issuecomment-433286729  

Understood.

---
