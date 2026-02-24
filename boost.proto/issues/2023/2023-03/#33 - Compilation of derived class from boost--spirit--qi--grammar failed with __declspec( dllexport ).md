# #33 - Compilation of derived class from boost::spirit::qi::grammar failed with __declspec( dllexport ) [Open]

> Username: aleksey-seleznev  
> Created at: 2023-03-15 12:58:00 UTC  
> Updated at: 2023-03-24 07:03:21 UTC  
> Url: https://github.com/boostorg/proto/issues/33  

Clang-cl.   
I am trying to implement a class that inherits from boost::spirit::qi::grammar and make it exportable in dynamic library:  
```  
#pragma once  
#include <impexp.h>  
#include <boost/spirit/include/qi.hpp>  
#include <boost/spirit/include/phoenix.hpp>  
  
using boost::spirit::ascii::space_type;  
struct EXPORT_DLLCLASS Grammar : boost::spirit::qi::grammar< char const*, space_type >  
{  
   Grammar()  
       : Grammar::base_type( expr )  
   {  
   }  
   boost::spirit::qi::rule< char const*, space_type > expr, term;  
};  
```  
I'm using `EXPORT_DLLCLASS` to specify `__attribute__( ( visibility( "default" ) ) )` or `__declspec( dllexport )` depending on the platform. Build succeeds under Linux platform, but I have problems with compilation under Windows platform with the following errors:  
```  
C:\.conan\oq2odq\1\include\boost/proto/extends.hpp(536,17):  error: call to implicitly-deleted default constructor of 'boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::spirit::qi::reference<const boost::spirit::qi::rule<const char *, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::space, boost::spirit::char_encoding::ascii> >, 0>, boost::spirit::unused_type, boost::spirit::unused_type, boost::spirit::unused_type> > >, 0>'  
              : proto_expr_()  
                ^  
C:\.conan\oq2odq\1\include\boost/proto/proto_fwd.hpp(377,16):  note: in instantiation of member function 'boost::proto::exprns_::extends<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::spirit::qi::reference<const boost::spirit::qi::rule<const char *, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::space, boost::spirit::char_encoding::ascii> >, 0>, boost::spirit::unused_type, boost::spirit::unused_type, boost::spirit::unused_type> > >, 0>, boost::spirit::qi::grammar<const char *, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::space, boost::spirit::char_encoding::ascii> >, 0>, boost::spirit::unused_type, boost::spirit::unused_type, boost::spirit::unused_type>, boost::proto::domainns_::default_domain, 0>::extends' requested here  
        struct extends;  
               ^  
C:\.conan\oq2odq\1\include\boost/proto/detail/preprocessed/expr_variadic.hpp(50,49):  note: default constructor of 'expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::spirit::qi::reference<const boost::spirit::qi::rule<const char *, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::spirit::tag::char_code<boost::spirit::tag::space, boost::spirit::char_encoding::ascii> >, 0>, boost::spirit::unused_type, boost::spirit::unused_type, boost::spirit::unused_type> > >, 0>' is implicitly deleted because field 'child0' has no default constructor  
        typedef Arg0 proto_child0; proto_child0 child0;  
```  
Build succeeds if `EXPORT_DLLCLASS` is not specified.  
https://stackoverflow.com/questions/75744429/compilation-of-derived-class-from-boostspiritqigrammar-failed-with-decls

---

## Comment 1

> Username: aleksey-seleznev  
> Created at: 2023-03-24 07:03:21 UTC  
> Url: https://github.com/boostorg/proto/issues/33#issuecomment-1482345392  

Any ideas?
