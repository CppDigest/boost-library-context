# #615 - Alternative operator attribute is incorrect [Closed]

> Username: atgraham12  
> Created at: 2020-07-11 18:52:04 UTC  
> Updated at: 2025-05-10 00:34:31 UTC  
> Closed at: 2025-05-10 00:34:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/615  

X3 compound attribute rules say this:  
  
> a: A, b: A --> (a | b): A  
  
However, when I write this simple bit of code (exposition purposes only):  
```cpp  
#include <boost/spirit/home/x3.hpp>  
int main() {  
    using namespace boost::spirit::x3;  
    auto const quoted_string = lexeme['"' >> *(char_ - '"') >> '"'];  
    auto value = std::string{};  
    auto assign_string = [&](auto& ctx){ value = _attr(ctx); };  
    auto const any_string = (quoted_string | quoted_string) [assign_string];  
    auto val = std::string_view{"\"Test\""};  
    phrase_parse(val.begin(), val.end(), any_string, space);  
}  
```  
I get this error:  
```  
error: no match for ‘operator=’ (operand types are ‘std::__cxx11::basic_string<char>’ and ‘boost::variant<boost::detail::variant::over_sequence<boost::mpl::l_item<mpl_::long_<2>, std::__cxx11::basic_string<char>, boost::mpl::l_item<mpl_::long_<1>, std::__cxx11::basic_string<char>, boost::mpl::l_end> > > >’)  
   7 |     auto assign_string = [&](auto& ctx){ value = _attr(ctx); };  
```  
That implies that the compound attribute rule is actually:  
  
> a: A, b: A --> (a | b): variant<A, A>  
  
Why is the attribute a variant instead of a string?

---

## Comment 1

> Username: Kojoley  
> Created at: 2020-07-11 19:04:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/615#issuecomment-657112919  

I have described the bug in #610, but if it was my will I just removed ability to create semantic actions on alternative parser, especially without an actual attribute. Or, at least, autorewrite `(a | b | c)[f]` to `a[f] | b[f] | c[f]`.
