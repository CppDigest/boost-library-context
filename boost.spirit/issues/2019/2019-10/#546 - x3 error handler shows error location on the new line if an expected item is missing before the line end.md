# #546 - x3 error handler shows error location on the new line if an expected item is missing before the line end [Closed]

> Username: maximiliank  
> Created at: 2019-10-17 10:17:12 UTC  
> Updated at: 2021-04-26 11:49:50 UTC  
> Closed at: 2021-04-26 11:49:50 UTC  
> Url: https://github.com/boostorg/spirit/issues/546  

If an expected item is missing before a line end, the location of the detailed error message is moved to the next line if there is a line end. Lets say you want to parse two integers separated by comma per line, then the following results in an  error message on the wrong line:  
  
```  
1  
3,4  
+++++++++++++++++++++++  
In line 2:  
Error! Expecting: delimiter ',' here:  
3,4  
^_  
```  
  
Instead I would expect:  
```  
In line 1:  
Error! Expecting: delimiter ',' here:  
1  
_^_  
```  
  
The reason is that the error_handler<Iterator>::skip_whitespace (which is called from error_handler<Iterator>::operator()) eats the '\n' and moves the position to the next line.  
  
An example can be found [here](https://godbolt.org/z/dWJhqF).

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-10-18 00:20:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/546#issuecomment-543421770  

Reduced:  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/utility/annotate_on_success.hpp>  
#include <boost/spirit/home/x3/support/utility/error_reporting.hpp>  
#include <iostream>  
  
namespace x3 = boost::spirit::x3;  
  
struct error_handler {  
  template <typename Iterator, typename Exception, typename Context>  
  x3::error_handler_result on_error(Iterator& first, Iterator const& last,  
                                    Exception const& x,  
                                    Context const& context) {  
    auto& error_handler = x3::get<x3::error_handler_tag>(context).get();  
    std::string message = "Error! Expecting: " + x.which() + " here:";  
    error_handler(x.where(), message);  
    return x3::error_handler_result::fail;  
  }  
};  
  
namespace boost::spirit::x3 {  
template <>  
struct get_info<int_type> {  
  std::string operator()(int_type const&) const { return "integral number"; }  
};  
}  // namespace boost::spirit::x3  
  
struct Line : error_handler, x3::annotate_on_success {};  
  
int main() {  
  std::string_view input = "x\nx456";  
  auto iter = input.cbegin();  
  auto const end = input.cend();  
  
  const auto line = x3::rule<struct Line>{"line"} = 'x' > x3::int_;  
  x3::error_handler<decltype(iter)> error_handler(iter, end, std::cout);  
  parse(iter, end, x3::with<x3::error_handler_tag>(std::ref(error_handler))[line % x3::eol]);  
}  
```
