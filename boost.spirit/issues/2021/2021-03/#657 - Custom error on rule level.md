# #657 - Custom error on rule level? [Closed]

> Username: arbinada-com  
> Created at: 2021-03-06 10:26:16 UTC  
> Updated at: 2021-06-01 19:24:47 UTC  
> Closed at: 2021-06-01 19:24:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/657  

Hello,  
is it possible to have a custom error handler on the rule level (for every rule in the limit) ?  
Cannot find this point in docs/examples.  
I.e.  
  
```  
DO WITH opt1 // should raise "Option opt1 is unknown"  
DO WITH opt2 = 123 // should raise "Invalid value 123 for option opt2"  
  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-03-06 22:51:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/657#issuecomment-792097154  

Did you miss https://www.boost.org/doc/libs/1_75_0/libs/spirit/doc/x3/html/spirit_x3/tutorials/error_handling.html?

---

## Comment 2

> Username: arbinada-com  
> Created at: 2021-03-06 23:01:11 UTC  
> Updated at: 2021-03-06 23:02:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/657#issuecomment-792101697  

Thank you for answer, I didn't miss this link. However, this example is "one error handler for a whole parser" wheras I need "one handler for every rule".

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-03-07 00:08:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/657#issuecomment-792126715  

I did not read the doc myself, maybe it is somewhat misleading, though examples (noting calc5/calc6) give a pretty much good picture.  
  
> this example is "one error handler for a whole parser" wheras I need "one handler for every rule"  
  
That's not true, it shows how to attach an error handler to a particular rule placeholder. Probably it is not trivial to extrapolate from one to many, so here you are:  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
namespace x3 = boost::spirit::x3;  
  
template <typename RuleID>  
struct my_error_handler  
{  
    template <typename Iterator, typename Exception, typename Context>  
    static x3::error_handler_result on_error(Iterator&, Iterator const&, Exception const&, Context const&)  
    {  
        std::cerr << "(" << typeid(RuleID).name() << " error handler fired) ";  
        return x3::error_handler_result::accept;  
    }  
};  
  
  
struct rule_a : my_error_handler<rule_a> {};  
struct rule_b : my_error_handler<rule_b> {};  
struct rule_grammar : my_error_handler<rule_grammar> {};  
  
x3::rule<rule_a> const a;  
x3::rule<rule_b> const b;  
x3::rule<rule_grammar> const grammar;  
  
auto const a_def = x3::expect[x3::double_];  
auto const b_def = x3::expect[x3::int_];  
auto const grammar_def = 'a' >> a | 'b' >> b | x3::expect['x'];  
  
BOOST_SPIRIT_DEFINE(a, b, grammar)  
  
int main()  
{  
    std::vector<std::string> tests = { "a 1.23", "b 123", "a .e-z", "b .0", "x", "d" };  
    for (auto& test: tests) {  
        auto s = test.data(), e = s + test.size();  
        std::cerr << "'" << test << "' ";  
        if (!phrase_parse(s, e, grammar, x3::space)) {  
            std::cerr << "parse error\n";  
        }  
        else {  
            std::cerr << "ok\n";  
        }  
    }  
}  
```  
  
To exclude any confusion, there are three different error handlers, each rule has its own.  
  
---  
  
There is also an `p.on_error(f)` method on every parser, though I do not know if it is documented.

---

## Comment 4

> Username: arbinada-com  
> Created at: 2021-03-07 12:43:02 UTC  
> Updated at: 2021-03-07 12:43:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/657#issuecomment-792273100  

Thank you for example, it's much more comprehensive now. Additional questions:  
- what is recommended way to inject some external dependency in error handler (i.e. custom message collector)?  
- how to acces to the last lexeme, the line number and the position of error?

---

## Comment 5

> Username: Kojoley  
> Created at: 2021-03-07 17:57:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/657#issuecomment-792323921  

I think these questions are covered in docs.  
  
> what is recommended way to inject some external dependency in error handler (i.e. custom message collector)?  
  
`x3::with<tag>(data)[p]` directive.  
  
> how to acces to the last lexeme, the line number and the position of error?  
  
`x3::error_handler<iterator_type>` implements such functionality, you can roll your own solution if it does not satisfy your needs.
