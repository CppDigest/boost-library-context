# #246 - Can't use a rule parameter to pass a symbol table to a rule? [Closed]

> Username: jwwalker  
> Created at: 2025-07-02 21:23:26 UTC  
> Updated at: 2025-07-13 20:49:41 UTC  
> Closed at: 2025-07-13 20:49:41 UTC  
> Url: https://github.com/boostorg/parser/issues/246  

I tried the following, and the compiler wasn't happy about it.  I guess the tutorial's discussion of parameters was too complicated for me.  
  
```c++  
#include <boost/parser/parser.hpp>  
  
namespace bp = ::boost::parser;  
  
bp::rule< struct toprule, double > toprule = "toprule";  
auto const toprule_def = bp::double_ >> bp::_p<0>;  
BOOST_PARSER_DEFINE_RULES( toprule );  
  
static void ParamTest( const bp::symbols<double>& syms )  
{  
    auto result = bp::parse( "10.3", toprule.with( syms ), bp::ws );  
}  
```  
  
By the way, am I correct that a rule must be defined at global scope?  
  
See also a discussion on [Stack Overflow](https://stackoverflow.com/questions/79684106/how-can-i-pass-a-parameter-to-a-parser-in-boost-parser), where a reply said "you're looking for lazy rule references" but I don't really know what that means.

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-07-13 20:49:41 UTC  
> Url: https://github.com/boostorg/parser/issues/246#issuecomment-3067294084  

> I tried the following, and the compiler wasn't happy about it. I guess the tutorial's discussion of parameters was too complicated for me.  
>   
> #include <boost/parser/parser.hpp>  
>   
> namespace bp = ::boost::parser;  
>   
> bp::rule< struct toprule, double > toprule = "toprule";  
> auto const toprule_def = bp::double_ >> bp::_p<0>;  
> BOOST_PARSER_DEFINE_RULES( toprule );  
>   
> static void ParamTest( const bp::symbols<double>& syms )  
> {  
>     auto result = bp::parse( "10.3", toprule.with( syms ), bp::ws );  
> }  
  
This is not a use case parameters are suited to; you can't pass parsers as parameters.  
  
> By the way, am I correct that a rule must be defined at global scope?  
  
Almost.  Namespace scope, not necessarily the global namespace.  
   
> See also a discussion on [Stack Overflow](https://stackoverflow.com/questions/79684106/how-can-i-pass-a-parameter-to-a-parser-in-boost-parser), where a reply said "you're looking for lazy rule references" but I don't really know what that means.  
  
Yeah, this came up in response to another request.  I spent a really long time trying to figure out how to make a lazy rule work, but it's simply not possible given the current design.  
  
The good news is that you're only real limitation is that, without lazy parsers, you cannot use a placeholder for one of an *open* set of possible parsers.  However, you can still use one of a *closed* set of parsers by building the logic into your parser for which one to use, based on some parameterization.  
  
For instance, you could write a parser that contains `bp::if_(bp::_p<0>)[p1][p2]` (or use `bp::switch_` for more alternatives).
