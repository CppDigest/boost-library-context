# #12 - MSVC C4180 warning [Open]

> Username: Kojoley  
> Created at: 2019-01-18 13:27:56 UTC  
> Updated at: 2019-01-18 13:27:56 UTC  
> Url: https://github.com/boostorg/proto/issues/12  

Code:  
```cpp  
#include <boost/spirit/include/qi_parse.hpp>  
#include <boost/spirit/include/qi_action.hpp>  
#include <boost/spirit/include/qi_numeric.hpp>  
  
void fun(int) {}  
  
int main()  
{  
    namespace qi = boost::spirit::qi;  
    char const *s = "42", *e = s + std::strlen(s);  
    qi::parse(s, e, qi::int_[fun]);  
}  
```  
  
Warning message:  
```  
1>boost/proto/extends.hpp(552): warning C4180: qualifier applied to function type has no meaning; ignored  
1>main.cpp(11): note: see reference to function template instantiation 'const boost::proto::exprns_::expr<Tag,Args,2> boost::proto::exprns_::extends<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<T>,0>,boost::spirit::terminal<boost::spirit::tag::int_>,boost::proto::domainns_::default_domain,0>::operator []<void(int)>(A (__cdecl &)) const' being compiled  
1>        with  
1>        [  
1>            Tag=boost::proto::tagns_::tag::subscript,  
1>            Args=boost::proto::argsns_::list2<const boost::spirit::terminal<boost::spirit::tag::int_> &,boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<void (__cdecl &)(int)>,0>>,  
1>            T=boost::spirit::tag::int_,  
1>            A=void (int)  
1>        ]  
1>main.cpp(11): note: see reference to function template instantiation 'const boost::proto::exprns_::expr<Tag,Args,2> boost::proto::exprns_::extends<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<T>,0>,boost::spirit::terminal<boost::spirit::tag::int_>,boost::proto::domainns_::default_domain,0>::operator []<void(int)>(A (__cdecl &)) const' being compiled  
1>        with  
1>        [  
1>            Tag=boost::proto::tagns_::tag::subscript,  
1>            Args=boost::proto::argsns_::list2<const boost::spirit::terminal<boost::spirit::tag::int_> &,boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal,boost::proto::argsns_::term<void (__cdecl &)(int)>,0>>,  
1>            T=boost::spirit::tag::int_,  
1>            A=void (int)  
1>        ]  
```  
  
MWE:  
```cpp  
#define BOOST_PROTO_DONT_USE_PREPROCESSED_FILES  
#include <boost/proto/proto.hpp>  
  
struct placeholder {};  
  
void fun() {}  
  
namespace proto = boost::proto;  
  
template<typename Expr>  
struct eterm : proto::extends<Expr, eterm<Expr> >  
{  
    typedef proto::extends<Expr, eterm<Expr> > base_type;  
      
    eterm(Expr const& expr = Expr())  
        : base_type(expr)  
    {}  
  
    BOOST_PROTO_EXTENDS_USING_ASSIGN(eterm)  
};  
  
int main()  
{  
    {  
        proto::terminal<placeholder>::type term;  
        term[fun]; // produces C4180 warning  
        term(fun); // produces C4180 warning  
        term = fun; // produces C4180 warning  
        term + fun;  // fine  
    }  
    {  
        eterm<proto::terminal<placeholder>::type> term;  
        term[fun]; // produces C4180 warning  
        term(fun); // produces C4180 warning  
        term = fun; // produces C4180 warning  
        term + fun;  // fine  
    }  
}  
```
