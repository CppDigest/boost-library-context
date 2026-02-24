# #716 - X3: plain into variant of single element tuple [Open]

> Username: Bockeman  
> Created at: 2022-01-23 23:37:05 UTC  
> Updated at: 2025-05-09 23:40:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/716  

This code does not compile  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
namespace x3		= boost::spirit::x3;  
template <typename T>  
  struct as_type{  
    template <typename E>  
    constexpr auto operator[](E e) const {return x3::rule<struct _, T> {} = e;}  
  };  
template <typename T>  
  static inline constexpr as_type<T> as;  
namespace ast {  
  using namespace std;  
  struct tagged_string	: std::string	{};  
  enum token_enum	{lcomment, rcomment};  
  struct tagged_token	{token_enum token;};  
  struct comment_item	: x3::variant<tagged_string, tagged_token>  
    {using base_type::base_type;using base_type::operator=;};  
  struct comment	: std::vector<comment_item>{  
    using std::vector<comment_item>::vector;  
    using std::vector<comment_item>::operator=;  
  };  
  struct start_item	: x3::variant<comment>  
    {using base_type::base_type; using base_type::operator=;};  
  struct start		: std::vector<start_item>{using std::vector<start_item>::vector;};  
}  
BOOST_FUSION_ADAPT_STRUCT(ast::tagged_token,		token)  
namespace grammar {  
  x3::symbols<ast::token_enum>	lcomment_token;  
  x3::symbols<ast::token_enum>	rcomment_token;  
  x3::rule<struct lcomment,	ast::tagged_token>	const tagged_lcomment	= "tagged_lcomment";  
  x3::rule<struct rcomment,	ast::tagged_token>	const tagged_rcomment	= "tagged_rcomment";  
  x3::rule<struct text,		ast::tagged_string>	const tagged_text	= "tagged_text";  
  x3::rule<struct comment,	ast::comment>		const comment		= "comment";  
  x3::rule<struct start_rule,	ast::start>		const start_rule	= "start_rule";  
  
  auto const tagged_lcomment_def	= lcomment_token;				// /*  
  auto const tagged_rcomment_def	= rcomment_token;				// */  
  auto const tagged_text_def		= *(x3::char_ - rcomment_token);		// not */  
    
  // Compile fails:  
  auto const comment_def	= lcomment_token > tagged_text > rcomment_token;			// /* ... */  
    
  // Compiles ok, but ast is not correctly assembled:  
  //auto const comment_def	= (x3::rule<class id>{}=lcomment_token) > tagged_text > tagged_rcomment;// /* ... */  
  
  // Compiles ok, and ast is ok:  
  //auto const comment_def	= as<ast::tagged_token>[lcomment_token] > tagged_text > tagged_rcomment;// /* ... */  
  //auto const comment_def	= tagged_lcomment > tagged_text > tagged_rcomment;			// /* ... */  
    
  auto const start_rule_def	= x3::no_skip[+comment];	// 1 ... ;  
    
  BOOST_SPIRIT_DEFINE(tagged_lcomment, tagged_rcomment, tagged_text, comment, start_rule);  
}  
int main() {  
  char const*			iter	= "/** /**/", * const end	= iter + std::strlen(iter);  
  ast::start			ast;  
  grammar::lcomment_token.add	("/*",		ast::lcomment);  
  grammar::rcomment_token.add	("*/",		ast::rcomment);  
  bool ok			= parse(iter, end, grammar::start_rule, ast) && iter==end;  
  if(ok)BOOST_ASSERT(3==boost::get<ast::comment>(ast[0].var).size());  
  return !ok;  
}  
```  
It seems the only way to successfully compile is to wrap the token (`x3::symbols<>`) into a rule (either inline, or with a separate rule definition).  Is this an X3 implementation problem, or is it beyond the anticipated capabilities of X3?

---

## Comment 1

> Username: Kojoley  
> Created at: 2022-01-24 02:42:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/716#issuecomment-1019661661  

Don't know variant or single element tuple or both, but not x3::symbols:  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
namespace ast {  
    struct tagged_token { int token; };  
}  
BOOST_FUSION_ADAPT_STRUCT(ast::tagged_token, token)  
  
int main() {  
    namespace x3 = boost::spirit::x3;  
    char const* iter = "/** /**/", * const end = iter + std::strlen(iter);  
    x3::variant<ast::tagged_token> x;  
    x3::parse(iter, end, x3::attr(int{}), x);  
}  
```

---

## Comment 2

> Username: bfueldner  
> Created at: 2023-07-20 14:52:03 UTC  
> Updated at: 2023-07-20 14:52:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/716#issuecomment-1644070501  

It seems that not the single element variant is the problem. It depends on the number of struct/class members. No member works, two or more members works but not one member:  
  
```c++  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
#include <variant>  
  
struct no_member {};  
  
struct one_member {  
    int a;  
};  
BOOST_FUSION_ADAPT_STRUCT(one_member, a)  
  
struct two_members {  
    int a{};  
    int b{};  
};  
BOOST_FUSION_ADAPT_STRUCT(two_members, a, b)  
  
int main()  
{  
    namespace x3 = boost::spirit::x3;  
  
    char const* iter = "", * const end = iter + std::strlen(iter);  
    std::variant<no_member, one_member, two_members> x;  
  
    x3::parse(iter, end, x3::attr(no_member{}), x); // This line compiles  
    x3::parse(iter, end, x3::attr(one_member{}), x); // This line failes  
    x3::parse(iter, end, x3::attr(two_members{}), x); // This line compiles  
}  
```  
  
Is there a workaround available for this behaviour?

---

## Comment 3

> Username: cppljevans  
> Created at: 2023-07-21 03:05:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/716#issuecomment-1644921228  

Use boost::variant instead of std::variant.    
The problem, I'd guess, is:  
   https://www.boost.org/doc/libs/1_81_0/boost/spirit/home/x3/support/traits/is_variant.hpp  
doesn't consider std::variant as a variant.

---

## Comment 4

> Username: cppljevans  
> Created at: 2023-07-21 03:30:21 UTC  
> Updated at: 2023-07-21 03:33:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/716#issuecomment-1644934716  

OOPS!  Adding obvious change to is_variant.hpp not enough.  With the obvious change, now getting, using std::variant:  
```  
C:/msys64/home/evansl/prog_dev/boost.org/1_80_0download/download/boost/spirit/home/x3/support/traits/variant_has_substitute.hpp:23:40: error: no type named 'types' in 'std::variant<no_member, one_member, two_members>'  
        typedef typename variant_type::types types;  
                ~~~~~~~~~~~~~~~~~~~~~~~^~~~~  
```

---

## Comment 5

> Username: cppljevans  
> Created at: 2023-07-21 04:38:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/716#issuecomment-1644970531  

> OOPS! Adding obvious change to is_variant.hpp not enough. With the obvious change, now getting, using std::variant:  
>   
> ```  
> C:/msys64/home/evansl/prog_dev/boost.org/1_80_0download/download/boost/spirit/home/x3/support/traits/variant_has_substitute.hpp:23:40: error: no type named 'types' in 'std::variant<no_member, one_member, two_members>'  
>         typedef typename variant_type::types types;  
>                 ~~~~~~~~~~~~~~~~~~~~~~~^~~~~  
> ```  
  
However, changing obvious portion of variant_has_substitute.hpp to:  
```     
    template <typename Variant, typename T>  
    struct variant_has_substitute_impl  
    ;  
    template <template<typename...>typename Variant, typename T, typename... Elements>  
    struct variant_has_substitute_impl< Variant<Elements...>, T>  
    {  
        // Find a type from the Variant that can be a substitute for T.  
        // return true_ if one is found, else false_  
  
        typedef Variant<Elements...> variant_type;  
        typedef fusion::vector<Elements...> types;  
```  
and #including <boost/fusion/container.hpp> before seems to solve problem.

---

## Comment 6

> Username: bfueldner  
> Created at: 2023-07-24 10:00:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/716#issuecomment-1647599990  

@cppljevans Thank you for your response. Replacing `std::variant` with `boost::variant` (or `x3::variant`) did the trick!  
  
Sorry for confusing with the original post of @Bockeman.
