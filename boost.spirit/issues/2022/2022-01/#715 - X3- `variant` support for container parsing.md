# #715 - X3: `variant` support for container parsing [Open]

> Username: Bockeman  
> Created at: 2022-01-23 20:36:53 UTC  
> Updated at: 2025-05-09 23:40:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/715  

This code fails to compile:  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
namespace x3		= boost::spirit::x3;  
namespace ast {  
  struct tagged_string	: std::string	{};  
  enum token_enum	{lcomment, rcomment};  
  struct tagged_token	{token_enum token;};  
  struct comment_item	: x3::variant<tagged_string, tagged_token>  
    {using base_type::base_type;using base_type::operator=;};  
  struct comment	: std::vector<comment_item>{};  
  struct start_item	: x3::variant<comment>  
    {using base_type::base_type; using base_type::operator=;};  
  struct start		: std::vector<start_item>	{using std::vector<start_item>::vector;};  
}  
BOOST_FUSION_ADAPT_STRUCT(ast::tagged_token,		token)  
namespace grammar {  
  x3::symbols<ast::token_enum>	lcomment_token;  
  x3::symbols<ast::token_enum>	rcomment_token;  
  x3::rule<struct lcomment,	ast::tagged_token>	const lcomment		= "lcomment";  
  x3::rule<struct rcomment,	ast::tagged_token>	const rcomment		= "rcomment";  
  x3::rule<struct comment,	ast::comment>		const comment		= "comment";  
  x3::rule<struct start_rule,	ast::start>		const start_rule	= "start_rule";  
  
  auto const lcomment_def	= lcomment_token;				// /*  
  auto const rcomment_def	= rcomment_token;				// */  
    
  // Compile fails:  
  auto const comment_def	= lcomment >> *(x3::char_ - rcomment) >> rcomment;	// /* ... */  
  
  // Compiles ok:  
  //auto const comment_def	= lcomment >> (x3::rule<class id>{}=*(x3::char_ - rcomment)) >> rcomment;	// /* ... */  
  
  auto const start_rule_def	= x3::no_skip[+comment];	// 1 ... ;  
    
  BOOST_SPIRIT_DEFINE(lcomment, rcomment, comment, start_rule);  
}  
int main() {  
  char const*			iter	= "/*c*/", * const end	= iter + std::strlen(iter);  
  ast::start			ast;  
  grammar::lcomment_token.add	("/*",		ast::lcomment);  
  grammar::rcomment_token.add	("*/",		ast::rcomment);  
  return !parse(iter, end, grammar::start_rule, ast) || iter!=end;  
}  
```  
But if the text between the delimiters is wrapped as a separate rule, then it does compile.  
  
_This is a simplified version, where in reality `tagged_string` is not just a std::string but contains addition tagging information._

---

## Comment 1

> Username: Kojoley  
> Created at: 2022-01-24 02:06:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/715#issuecomment-1019645339  

That's about `variant` support for containers parsing:  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
int main() {  
    namespace x3 = boost::spirit::x3;  
    char const* iter = "/*c*/", * const end = iter + std::strlen(iter);  
    x3::variant<std::string, bool> x;  
    x3::parse(iter, end, +x3::char_, x);  
}  
```
