# #659 - x3::eps with a semantic action breaks parsing into structs [Closed]

> Username: syyyr  
> Created at: 2021-03-08 11:01:09 UTC  
> Updated at: 2021-03-08 13:17:26 UTC  
> Closed at: 2021-03-08 13:17:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/659  

Hi, I'm trying to get rid of fusion stuff in my single-element structs as suggested in #463.  
While doing that, I found a bug with semantic actions, where the struct doesn't get correctly filled with the value. This is how to reproduce.  
```cpp  
#include <iostream>  
#include <boost/spirit/home/x3.hpp>  
  
namespace x3 = boost::spirit::x3;  
  
struct number {  
  int value;  
  number& operator=(const int data) {  
      this->value = data;  
      return *this;  
  }  
};  
  
auto doSideEffects = x3::eps[([] (auto& ctx) {  
    // do some stuff...  
})];  
  
auto wrappedDoSideEffects = x3::rule<class Rule, x3::unused_type>{} = doSideEffects;  
  
auto my_rule = x3::rule<class my_class, number>{} = doSideEffects > x3::int32;  
  
int main(int argc, char* argv[])  
{  
    std::string input = "235";  
    number out;  
    auto it = input.begin();  
    auto res = x3::parse(it, input.end(), my_rule, out);  
    // This doesn't print 235, because doSideEffects breaks the parser.  
    // If I swap doSideEffects with wrappedDoSideEffects, it works again.  
    std::cout << "out.arg = " << out.value << "\n";  
  
    return 0;  
}  
```  
My `operator=` actually never gets called. I'm not sure I implemented it correctly. Is this really a bug or am I doing something wrong?  
I have also tried adding another member into the struct and use BOOST_FUSION_ADAPT_STRUCT and the bug is still there: https://gist.github.com/syyyr/94f7f9a0b65e8d4a81eb7a55be8e62ff  
  
Boost version: 1.75.0

---

## Comment 1

> Username: djowel  
> Created at: 2021-03-08 11:26:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/659#issuecomment-792690560  

Semantic actions on a rule definition inhibit automatic attribute propagation. See:  
  
https://stackoverflow.com/questions/33929849/boost-spirit-x3-ast-not-working-with-semantic-actions-when-using-separate-rule-d

---

## Comment 2

> Username: syyyr  
> Created at: 2021-03-08 13:09:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/659#issuecomment-792745071  

So, the function of `operator%=` is to automatically propagate attributes even if I'm using semantic actions?

---

## Comment 3

> Username: syyyr  
> Created at: 2021-03-08 13:17:03 UTC  
> Updated at: 2021-03-08 13:17:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/659#issuecomment-792749462  

Okay, found it in the documentation of Qi. https://www.boost.org/doc/libs/1_75_0/libs/spirit/doc/html/spirit/qi/reference/nonterminal/rule.html
