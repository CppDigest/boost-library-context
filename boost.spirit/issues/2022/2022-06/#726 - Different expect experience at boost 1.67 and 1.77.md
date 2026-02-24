# #726 - Different expect[] experience at boost 1.67 and 1.77 [Closed]

> Username: ibis-hdl  
> Created at: 2022-06-17 18:09:29 UTC  
> Updated at: 2022-08-17 06:35:07 UTC  
> Closed at: 2022-08-17 06:35:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/726  

I had to move to more up-to-date boost library and some of my unit test failed. I high-jack the reduced example from [x3 error handler shows error location on the new line if an expected item is missing before the line end #546](https://github.com/boostorg/spirit/issues/546):  
  
```  
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
    if(first == x.where()) {  
        std::cout << "first == x.where\n";  
    } else {  
        std::cout << "first != x.where\n";  
    }  
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
  
Running this at godbolt.org with [boost 1.76](https://godbolt.org/z/TPjb6rMTq) shows the expected result  
```  
first != x.where  
In line 2:  
Error! Expecting: integral number here:  
x456  
^_  
```  
while compiling with [1.77](https://godbolt.org/z/j3PzTM4Pr) and later, the result differs:  
```  
first == x.where  
In line 1:  
Error! Expecting: integral number here:  
x  
_^_  
```  
which explains my failed test and probably other issues faced here.  
  
For my understanding, first points to the last successfully parsed subject (end of subject) and x.where() to the expectation point (depend on use of skipper etc.), isn't it?

---

## Comment 1

> Username: ibis-hdl  
> Created at: 2022-08-16 18:56:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/726#issuecomment-1217025144  

Anyone with ideas what happens here?

---

## Comment 2

> Username: Kojoley  
> Created at: 2022-08-16 19:26:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/726#issuecomment-1217067691  

The location immediately after 'x' symbol is the correct one in your example; a number is expected, but '\n' is found.

---

## Comment 3

> Username: ibis-hdl  
> Created at: 2022-08-17 06:35:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/726#issuecomment-1217521594  

thank you, so I have to fix my error handling code this way.
