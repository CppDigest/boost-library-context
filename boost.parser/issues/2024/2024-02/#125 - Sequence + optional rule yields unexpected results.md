# #125 - Sequence + optional rule yields unexpected results [Closed]

> Username: anarthal  
> Created at: 2024-02-27 19:25:01 UTC  
> Updated at: 2024-03-15 21:29:35 UTC  
> Closed at: 2024-03-15 21:29:35 UTC  
> Url: https://github.com/boostorg/parser/issues/125  

The following program  
  
```  
#include <boost/parser/parser.hpp>  
  
#include <optional>  
  
namespace bp = boost::parser;  
  
constexpr auto print_repl_field = [](auto& ctx) {  
    const std::optional<unsigned short>& val = bp::_attr(ctx);  
    if (val)  
    {  
        std::cout << "Found integer: " << *val << std::endl;  
    }  
    else  
    {  
        std::cout << "No integer found\n";  
    }  
};  
  
constexpr auto replacement_field_def = bp::lit('{') >> -bp::ushort_;  
  
constexpr bp::rule<struct replacement_field_rule, std::optional<unsigned short>>  
    replacement_field = "replacement_field";  
  
BOOST_PARSER_DEFINE_RULES(replacement_field)  
  
int main(int argc, char** argv) { bp::parse(argv[1], replacement_field[print_repl_field]); }  
```  
  
Yields the following  
  
```  
./build/main '{9'  
Found integer: 9 // correct  
  
./build/main '{'  
Found integer: 0 // incorrect  
```  
  
Doesn't happen if I remove the rule and make it a regular parser.  
  
Compiler: gcc-12, debug, under Linux.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-27 23:37:50 UTC  
> Url: https://github.com/boostorg/parser/issues/125#issuecomment-1967915077  

Interesting.  Thanks for reporting this!  I'll investigate.
