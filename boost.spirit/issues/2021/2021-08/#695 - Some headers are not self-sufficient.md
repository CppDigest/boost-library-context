# #695 - Some headers are not self-sufficient [Closed]

> Username: slymz  
> Created at: 2021-08-20 01:50:00 UTC  
> Updated at: 2021-08-28 20:43:47 UTC  
> Closed at: 2021-08-28 20:43:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/695  

See https://godbolt.org/z/rjdGMeTMn  
  
This one-liner  
  
```  
#include <boost/spirit/repository/include/qi_keywords.hpp>  
```  
  
Fails with:  
  
```  
error C3878: syntax error: unexpected token 'identifier' following 'expression'  
note: This diagnostic occurred in the compiler generated function 'bool boost::spirit::repository::qi::detail::parse_dispatcher<Elements,Iterator,Context,Skipper,Flags,Counters,Attribute,NoCasePass>::call_subject_unused(const Subject &,Iterator &,const Iterator &,Context &,const Skipper &,Index &) const'  
 note: see reference to class template instantiation 'boost::spirit::repository::qi::detail::parse_dispatcher<Elements,Iterator,Context,Skipper,Flags,Counters,Attribute,NoCasePass>' being compiled  
  
[..]  
```

---

## Comment 1

> Username: slymz  
> Created at: 2021-08-20 02:04:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/695#issuecomment-902373703  

.. but with the following  two extra headers, it does:  
  
```  
#include <boost/fusion/algorithm/query/any.hpp>  
#include <boost/spirit/repository/home/qi/directive/kwd.hpp>  
  
#include <boost/spirit/repository/include/qi_keywords.hpp>  
```  
  
https://godbolt.org/z/14W774fsW
