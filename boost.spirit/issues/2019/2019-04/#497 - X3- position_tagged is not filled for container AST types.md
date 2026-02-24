# #497 - X3: position_tagged is not filled for container AST types [Closed]

> Username: Xeverous  
> Created at: 2019-04-24 16:47:05 UTC  
> Updated at: 2019-04-25 12:38:40 UTC  
> Closed at: 2019-04-25 03:19:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/497  

Hello, it's me again.  
  
repro: https://wandbox.org/permlink/BwnJubJ7NTQl2UAW  
  
To be more precise: position tag members in AST types that both inherit from `x3::position_tagged` and are containers do not get filled. `position_cache.get_positions.size()` is smaller than expected.

---

## Comment 1

> Username: Xeverous  
> Created at: 2019-04-24 17:00:04 UTC  
> Url: https://github.com/boostorg/spirit/issues/497#issuecomment-486333259  

Reduced:  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/position_tagged.hpp>  
#include <vector>  
#include <string_view>  
#include <iostream>  
  
namespace x3 = boost::spirit::x3;  
  
namespace ast  
{  
  
struct arguments : std::vector<int>, x3::position_tagged  
{};  
  
}  
  
x3::rule<class arguments_class, ast::arguments> arguments;  
auto const arguments_def = x3::int_ % ',';  
BOOST_SPIRIT_DEFINE(arguments)  
  
using iterator_type = std::string_view::const_iterator;  
using range_type = boost::iterator_range<iterator_type>;  
using position_cache_type = x3::position_cache<std::vector<iterator_type>>;  
  
int main()  
{  
	const std::string_view input = "1, 2, 3";  
	      iterator_type it   {input.begin()};  
	const iterator_type begin{input.begin()};  
	const iterator_type end  {input.end()};  
	position_cache_type position_cache(begin, end);  
  
	const auto parser = x3::with<struct position_cache_tag>(std::ref(position_cache))[arguments];  
  
	ast::arguments ast;  
	const bool result = x3::phrase_parse(  
		it,  
		end,  
		parser,  
		x3::space,  
		ast);  
  
	if (!result)  
		std::cout << "parsing failed\n";  
  
	std::cout << position_cache.get_positions().size() << "\n";  
}  
```  
  
Expected output: 1  
  
Actual output: 0

---

## Comment 2

> Username: djowel  
> Created at: 2019-04-25 03:17:24 UTC  
> Updated at: 2019-04-25 03:24:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/497#issuecomment-486504990  

OK, I assumed that the code was correct. After looking, it all boils down to incorrect usage. For documentation and an example of the correct usage see:  
   
https://www.boost.org/doc/libs/1_69_0/libs/spirit/doc/x3/html/spirit_x3/tutorials/annotation.html  
  
I'll remove my comments above. They are irrelevant.  
  
Here's the correct code:  
  
```c++  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/position_tagged.hpp>  
#include <vector>  
#include <string_view>  
#include <iostream>  
  
namespace x3 = boost::spirit::x3;  
  
namespace ast  
{  
     
   struct arguments : std::vector<int>, x3::position_tagged  
   {};  
     
}  
  
struct position_cache_tag;  
  
struct annotate_position  
{  
   template <typename T, typename Iterator, typename Context>  
   inline void on_success(Iterator const& first, Iterator const& last  
                          , T& ast, Context const& context)  
   {  
      auto& position_cache = x3::get<position_cache_tag>(context).get();  
      position_cache.annotate(ast, first, last);  
   }  
};  
  
struct arguments_class : annotate_position {};  
  
x3::rule<arguments_class, ast::arguments> arguments;  
auto const arguments_def = x3::int_ % ',';  
BOOST_SPIRIT_DEFINE(arguments)  
  
using iterator_type = std::string_view::const_iterator;  
using range_type = boost::iterator_range<iterator_type>;  
using position_cache_type = x3::position_cache<std::vector<iterator_type>>;  
  
  
  
int main()  
{  
   const std::string_view input = "1, 2, 3";  
   iterator_type it   {input.begin()};  
   const iterator_type begin{input.begin()};  
   const iterator_type end  {input.end()};  
   position_cache_type position_cache(begin, end);  
     
   const auto parser = x3::with<position_cache_tag>(std::ref(position_cache))[arguments];  
     
   ast::arguments ast;  
   const bool result = x3::phrase_parse(  
                                        it,  
                                        end,  
                                        parser,  
                                        x3::space,  
                                        ast);  
     
   if (!result)  
      std::cout << "parsing failed\n";  
     
   std::cout << position_cache.get_positions().size() << "\n";  
}  
```

---

## Comment 3

> Username: Xeverous  
> Created at: 2019-04-25 12:38:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/497#issuecomment-486655081  

Ok, I have checked it, the bug was indeed in my code. Some grammar objects had duplicated code which made them bypass the need for a sub-grammar where that subgrammar did not have any associated rule ID.
