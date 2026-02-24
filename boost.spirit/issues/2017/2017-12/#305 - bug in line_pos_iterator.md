# #305 - bug in line_pos_iterator [Closed]

> Username: octopus-prime  
> Created at: 2017-12-01 13:58:55 UTC  
> Updated at: 2017-12-07 10:43:02 UTC  
> Closed at: 2017-12-07 10:43:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/305  

Running this code  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/include/support_line_pos_iterator.hpp>  
#include <string>  
#include <iostream>  
#include <iomanip>  
  
namespace x3 = boost::spirit::x3;  
  
int main() {  
    using iterator_t = boost::spirit::line_pos_iterator<std::string::const_iterator>;  
  
    std::string const input{"foo\nfoo\ngit"};  
    iterator_t const begin{input.begin()};  
    iterator_t const end{input.end()};  
  
    try {  
        x3::phrase_parse(begin, end, x3::lit("foo") > x3::lit("bar") > x3::lit("git"), x3::space);  
    }  
    catch (x3::expectation_failure<iterator_t> const& exception) {  
        boost::iterator_range<iterator_t> const range = get_current_line(begin, exception.where(), end);  
        std::string const current(range.begin(), range.end());  
        std::cout << "line: " << get_line(exception.where()) << std::endl;  
        std::cout << "column: " << get_column(begin, exception.where()) << std::endl;  
        std::cout << "given: " << std::quoted(current) << std::endl;  
        std::cout << "wanted: " << exception.which() << std::endl;  
    }  
    return 0;  
}  
```  
  
gives this output:  
  
```  
line: 2  
column: 2  
given: "  
foo"  
wanted: "bar"  
```  
  
This is a bug, because the expected output is:  
  
```  
line: 2  
column: 1  
given: "foo"  
wanted: "bar"  
```
