# #21 - Proposal for value-based Interface in Boost.Metaparse [Open]

> Username: denzor200  
> Created at: 2025-12-12 00:17:51 UTC  
> Updated at: 2025-12-12 23:10:28 UTC  
> Url: https://github.com/boostorg/metaparse/issues/21  

**Motivation**  
  
Boost.Metaparse is a powerful library for compile-time parsing, but its interface remains largely centered around template metafunctions in the classic `typename ...` style. While the library has already taken a step toward value-based with `BOOST_METAPARSE_STRING_VALUE`—this improvement is only partial. In 2025, developers should not be forced to write verbose and archaic constructs like:  
  
```cpp  
typedef  
  foldl_reject_incomplete1<  
    one_of<  
      always_c<'.', r_any_char>,  
      transform<one_char_except_c<'.', '(', ')'>, r_char_lit>  
    >,  
    r_epsilon,  
    r_append  
  >  
  non_bracket_expr;  
```  
  
Such syntax is not only cumbersome and hard to read, but it also stands in stark contrast to modern C++ idioms. The existing value-based support for strings demonstrates the library's potential to evolve, but stopping halfway leaves the interface inconsistent and underutilized.  
  
I propose to **complete this evolution** by introducing a comprehensive, value-based C++11 interface for the entire library. This will align Boost.Metaparse with contemporary C++ practices, making it more accessible, expressive, and maintainable.  
  
**Proposed Solution**  
  
Extend the value-based approach consistently across all metafunctions by introducing lightweight `constexpr` wrapper functions. Each wrapper will accept value-based arguments (types as template parameters, callable objects, or already wrapped values) and return an instance of the corresponding Boost.Metaparse metafunction. For example:  
  
```cpp  
template<typename Left, typename Middle, typename Right>  
constexpr auto middle_of(Left, Middle, Right) ->   
    boost::metaparse::middle_of<Left, Middle, Right>  
{  
    return {};  
}  
```  
  
We have to negotiate the way how to ensure full backward compatibility and a clear distinction from the classic metafunction-based interface.  
As a temporary solution i've put all these wrappers into `boost::metaparse::cxx11`.  
  
**Benefits**  
  
1. **Consistency** – If `BOOST_METAPARSE_STRING_VALUE` enables value-based strings, why should the rest of the library remain type-based? A uniform value-based interface eliminates cognitive dissonance and simplifies learning.  
2. **Modern readability** – Complex parser definitions become clean, linear, and visually similar to runtime parsing code—lowering the barrier for new users.  
3. **Seamless integration with modern C++** – The wrappers leverage `constexpr`, `auto`, and lambda expressions, fitting naturally into C++11 and beyond.  
4. **Zero-cost abstraction** – The wrappers are trivial, header-only, and impose no compile-time or runtime overhead.  
5. **Progressive evolution** – This change complements rather than replaces the existing interface, allowing gradual adoption.  
  
**Example Transformation**  
  
With the proposed wrappers, the regexp example from the repository transforms into a much clearer and more maintainable form:  
```cpp  
#include "boost_metaparse_cxx11/wrappers.hpp"  
#include <iostream>  
#include <boost/xpressive/xpressive.hpp>  
  
using boost::metaparse::cxx11::foldl_reject_incomplete;  
using boost::metaparse::cxx11::foldl_reject_incomplete1;  
using boost::metaparse::cxx11::lit_c;  
using boost::metaparse::cxx11::transform;  
using boost::metaparse::cxx11::build_parser;  
using boost::metaparse::cxx11::one_of;  
using boost::metaparse::cxx11::always_c;  
using boost::metaparse::cxx11::middle_of;  
using boost::metaparse::cxx11::one_char_except_c;  
using boost::metaparse::cxx11::entire_input;  
  
using boost::xpressive::sregex;  
using boost::xpressive::as_xpr;  
  
void test_string(const std::string& s)  
{  
  /*  
  * Results of parsing  
  */  
  
  using boost::xpressive::sregex;  
  using boost::xpressive::as_xpr;  
  
  constexpr auto r_any_char = []() {  
    return boost::xpressive::_;  
  };  
  
  constexpr auto r_char_lit = [](char C) {  
    return as_xpr(C);  
  };  
  
  constexpr auto r_append = [](const sregex& lhs, const sregex& rhs) -> sregex {  
    return lhs >> rhs;  
  };  
  
  constexpr auto r_epsilon = []{  
    return as_xpr("");  
  };  
  
  using boost::xpressive::regex_match;  
  using boost::xpressive::smatch;  
  using std::cout;  
  using std::endl;  
  
  /*  
  * The grammar  
  *  
  * regexp ::= (bracket_expr | non_bracket_expr)*  
  * non_bracket_expr ::= '.' | char_lit  
  * bracket_expr ::= '(' regexp ')'  
  * char_lit ::= any character except: . ( )  
  */  
  
  constexpr auto non_bracket_expr = foldl_reject_incomplete1(  
    one_of(  
      always_c<'.'>(r_any_char),  
      transform(  
        one_char_except_c<'.', '(', ')'>(),  
        r_char_lit  
      )  
    ),  
    r_epsilon,  
    r_append  
  );  
  
  constexpr auto bracket_expr = middle_of(  
    lit_c<'('>(),  
    non_bracket_expr,  
    lit_c<')'>()  
  );  
  
  constexpr auto regexp = foldl_reject_incomplete(  
    one_of(  
      bracket_expr,  
      non_bracket_expr  
    ),  
    r_epsilon,  
    r_append  
  );  
  
  constexpr auto regexp_parser =  
    build_parser(entire_input(regexp));  
  
  const sregex re = regexp_parser(BOOST_METAPARSE_STRING_VALUE(".(bc)"));  
  
  smatch w;  
  
  cout << s << (regex_match(s, w, re) ? " matches " : " doesn't match ")  
    << ".(bc)" << endl;  
}  
  
int main()  
{  
  test_string("abc");  
  test_string("aba");  
  
  return 0;  
}  
```  
  
Working prototypes are available:  
- C++11 prototype: https://godbolt.org/z/8PP1Y9sn3    
- C++20 prototype: https://godbolt.org/z/EGP7xsaG7  
  
The wrapper implementation (sufficient for the regexp example) can be reviewed here:    
https://github.com/denzor200/boost_metaparse_cxx11/blob/main/wrappers.hpp  
  
**Implementation Outline**  
  
The change is non-breaking and can be introduced incrementally. It requires only the addition of header files containing wrapper functions for each metafunction—building naturally on the foundation laid by `BOOST_METAPARSE_STRING_VALUE`.  
  
**Conclusion**  
  
Boost.Metaparse has already begun moving toward a value-based interface. Let’s finish that journey. By extending this approach library-wide, we can modernize Boost.Metaparse, enhance its usability, and ensure it remains a first-choice tool for compile-time parsing in modern C++. I am eager to contribute a pull request if the idea is accepted.  
  
Thank you for your consideration.

---

## Comment 1

> Username: sabel83  
> Created at: 2025-12-12 21:13:32 UTC  
> Url: https://github.com/boostorg/metaparse/issues/21#issuecomment-3648159628  

Hi,  
  
This is a good direction, the library would benefit from such a new interface. Have you considered an example which is more complex than the regex one? For example how would you imagine the meta_metaparse example or the most complex one, the meta_hs?

---

## Comment 2

> Username: denzor200  
> Created at: 2025-12-12 23:10:28 UTC  
> Url: https://github.com/boostorg/metaparse/issues/21#issuecomment-3648463587  

> Hi,  
>   
> This is a good direction, the library would benefit from such a new interface. Have you considered an example which is more complex than the regex one? For example how would you imagine the meta_metaparse example or the most complex one, the meta_hs?  
  
Having looking at meta_metaparse, I believe it's easily possible to convert that example to value based. I will post it in one of these days.  
And regarding meta_metaparse.. Please give me more time to recognize this example:)
