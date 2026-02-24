# #498 - Integrate Boost.Describe into Boost.Hana? [Open]

> Username: denzor200  
> Created at: 2021-12-09 15:04:47 UTC  
> Updated at: 2021-12-09 15:04:47 UTC  
> Url: https://github.com/boostorg/hana/issues/498  

Briefly about **the Boost Describe library**:  
"The purpose of the library is to establish a standard way of providing these reflection abilities. Many existing libraries provide their own way of describing enums or classes, but without a standard, code written by different people cannot interoperate." © Peter Dimov https://www.boost.org/doc/libs/develop/libs/describe/doc/html/describe.html  
  
For example, this will allow _BOOST_DESCRIBE_STRUCT_ to be used instead of _BOOST_HANA_ADAPT_STRUCT_, which is more standarted way.  
I suggest to add _boost/hana/ext/boost/describe.hpp_, which will take over the integration with this library.
