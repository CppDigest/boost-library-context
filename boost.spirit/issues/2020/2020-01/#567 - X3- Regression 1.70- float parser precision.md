# #567 - X3: Regression 1.70: float parser precision [Closed]

> Username: gzauhar  
> Created at: 2020-01-15 12:59:51 UTC  
> Updated at: 2020-01-16 14:20:37 UTC  
> Closed at: 2020-01-16 14:20:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/567  

The following code is very simplified version of our math expression parser but it reveals regression introduced in 1.71.  
  
```c++  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <boost/variant/apply_visitor.hpp>  
#include <boost/variant/static_visitor.hpp>  
  
#include <iostream>  
#include <string>  
  
namespace x3 = boost::spirit::x3;  
  
using ExpressionType = x3::variant<double>;  
  
struct Expression : ExpressionType {  
    using base_type::base_type;  
    using base_type::operator=;  
};  
  
class ASTPrinter : public boost::static_visitor<void> {  
public:  
    void operator()(const double ast) const { printf("%e\n", ast); }  
};  
  
namespace grammar {  
  
x3::real_parser<double, x3::ureal_policies<double>> const number = {};  
x3::rule<class expression, Expression> const expression = "expression";  
  
auto const expression_def =  
        number;  
  
BOOST_SPIRIT_DEFINE(  
    expression  
);  
  
}  // namespace grammar  
  
int main() {  
    const std::string expression = "1e308";  // BAD  
    // const std::string expression = "1.0e308";  // GOOD  
    // const std::string expression = "1e307";  // GOOD  
    auto position = expression.cbegin();  
  
    Expression ast;  
    x3::phrase_parse(position, expression.cend(), grammar::expression, x3::space, ast);  
  
    ASTPrinter printer;  
    boost::apply_visitor(printer, ast);  
}  
```  
```  
$ g++ -I boost-1.70 test.cpp && ./a.out   
1.000000e+308  
$ g++ -I boost-1.71 test.cpp && ./a.out   
6.953216e-310  
```  
Are we misusing boost.spirit or there is a regression? This issue is blocking us from upgrading to 1.71 or newer.  
Thank you in advance!

---

## Comment 1

> Username: Kojoley  
> Created at: 2020-01-15 14:54:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/567#issuecomment-574695377  

```cpp  
#include <boost/spirit/home/x3.hpp>  
  
#include <iostream>  
#include <string>  
  
int main() {  
    namespace x3 = boost::spirit::x3;  
  
    const std::string expression = "1e308";  // BAD  
    // const std::string expression = "1.0e308";  // GOOD  
    // const std::string expression = "1e307";  // GOOD  
    auto position = expression.cbegin();  
  
    x3::real_parser<double, x3::ureal_policies<double>> const number = {};  
  
    double v;  
    x3::phrase_parse(position, expression.cend(), number, x3::space, v);  
    printf("%e\n", v);  
    return v == 1e308 ? 0 : 1;  
}  
```  
  
```  
>git bisect start boost-1.71.0 boost-1.70.0  
Bisecting: 25 revisions left to test after this (roughly 5 steps)  
[d93e2c143cb8c8e02dbca50cdaf39f8a28d5bfa0] Lit suport for 32-bit unicode code points  
  
>git bisect run issue567.bat  
running issue567.bat  
1.000000e+308  
Bisecting: 12 revisions left to test after this (roughly 4 steps)  
[90d03d9ae9cb16e0e43ea7e05f1e416d60d3f3ae] Fix for https://github.com/boostorg/spirit/issues/505  
running issue567.bat  
6.535756e-312  
Bisecting: 6 revisions left to test after this (roughly 3 steps)  
[747f7ebc41638bf1f2ed380b435bff35ce06d09f] deal with long double compares  
running issue567.bat  
6.535754e-312  
Bisecting: 2 revisions left to test after this (roughly 2 steps)  
[6b6f67720563c09a6edac49608eb801bb1c51ffd] cosmetic tweaks  
running issue567.bat  
1.000000e+308  
Bisecting: 0 revisions left to test after this (roughly 1 step)  
[5a001edc45a844c5d3328e454cc07c98caa75dc0] Added long_double parser  
running issue567.bat  
6.535755e-312  
Bisecting: 0 revisions left to test after this (roughly 0 steps)  
[793c070d1f8755399bc4ad1df59635102b7e2b57] Take advantage of constexpr. No more big hard coded table.  
running issue567.bat  
6.535761e-312  
793c070d1f8755399bc4ad1df59635102b7e2b57 is the first bad commit  
commit 793c070d1f8755399bc4ad1df59635102b7e2b57  
Author: djowel <djowel@gmail.com>  
Date:   Sat May 4 17:45:58 2019 +0800  
  
    Take advantage of constexpr. No more big hard coded table.  
  
:040000 040000 5c0f22d605a58ad3637ebb9a68f498c52ea7dc04 6e88bdba50a0845fec9ff494dc4ca7f0d7525b7a M      include  
bisect run success  
```  
bisects to 793c070d1f8755399bc4ad1df59635102b7e2b57

---

## Comment 2

> Username: djowel  
> Created at: 2020-01-15 15:47:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/567#issuecomment-574720461  

Thanks, @Kojoley. I'll look into it as soon as I can.

---

## Comment 3

> Username: djowel  
> Created at: 2020-01-15 15:53:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/567#issuecomment-574723332  

Ah imprecise computations! Of course. Mea culpa!

---

## Comment 4

> Username: djowel  
> Created at: 2020-01-15 15:56:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/567#issuecomment-574724637  

I supose we'll have to bring back the big table, unless there's a better suggestion.

---

## Comment 5

> Username: djowel  
> Created at: 2020-01-15 16:18:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/567#issuecomment-574734820  

OK, reverted in develop.

---

## Comment 6

> Username: Kojoley  
> Created at: 2020-01-15 17:59:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/567#issuecomment-574779408  

> I supose we'll have to bring back the big table, unless there's a better suggestion.  
  
Use `pow` or fiddle with `memcpy`, but neither of them is constexpr even in c++2a.

---

## Comment 7

> Username: Kojoley  
> Created at: 2020-01-16 14:20:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/567#issuecomment-575172511  

The fix will be in 1.73 release. Thanks for your report!
