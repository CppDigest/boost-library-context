# #307 - repeat_parser and std::string attribute combination [Closed]

> Username: aclex  
> Created at: 2025-12-24 15:05:41 UTC  
> Updated at: 2025-12-27 16:02:59 UTC  
> Closed at: 2025-12-27 07:35:27 UTC  
> Url: https://github.com/boostorg/parser/issues/307  

I'm trying to create a rule for a string consisting of specified substrings and I seem to fail with it. Here's a simplified example:  
```c++  
#include <iostream>  
  
#include <boost/parser/parser.hpp>  
  
using namespace std;  
namespace bp = boost::parser;  
  
namespace  
{  
	namespace rule  
	{  
		constexpr boost::parser::rule<struct field_tag, string> field = "field";  
		constexpr auto field_def {*bp::string("AB")};  
		BOOST_PARSER_DEFINE_RULES(field);  
	}  
}  
  
int main()  
{  
	string ret;  
  
	string input{"ABABABAB"};  
	const auto result{bp::parse(input, rule::field, ret)};  
  
	cout << "Parsed successfully: " << result << endl;  
	cout << "Result: \"" << ret << "\"" << endl;  
  
	return 0;  
}  
```  
It fails to compile for me both with g++-15 and clang++-20 (I tried both `develop` and `master` branches). If I get it right, the repetition (from `*`) should result in sequence of `std::string` attributes, which I expect to collapse to one `std::string` attribute of the rule itself.  
  
Taking a look at the [attribute generation rules](https://www.boost.org/doc/libs/latest/doc/html/boost_parser/tutorial.html#boost_parser.tutorial.attribute_generation.sequence_parser_attribute_rules) I see there's no such exception for `std::string` directly, some similar exception concerns only `char`:  
> If C<T> is exactly std::string, and T is either char or char32_t, the combination yields a std::string.  
  
So I just wanted to ask if there's no such exception (for `std::string` sequence combined to single `std::string`) intentionally and I just miss something or that's something expected to work? Could you also please suggest a workaround, if possible, for such cases, when the grammar defines the form substrings being repeated? My real-world example is in fact parsing IPv6 address to a string.

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-12-27 07:35:27 UTC  
> Url: https://github.com/boostorg/parser/issues/307#issuecomment-3693796825  

Yes, the collapsing rules are all intentional -- including the one that says that std::strings should not combine by default.  I think you want to use `merge[]`.  
  
https://www.boost.org/doc/libs/latest/doc/html/boost_parser/tutorial.html

---

## Comment 2

> Username: aclex  
> Created at: 2025-12-27 16:02:59 UTC  
> Url: https://github.com/boostorg/parser/issues/307#issuecomment-3694062911  

Thank you for clarification! Indeed, `merge` looks like what I wanted, will try to use it.
