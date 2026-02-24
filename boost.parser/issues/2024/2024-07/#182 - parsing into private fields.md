# #182 - parsing into private fields [Open]

> Username: Jannik2099  
> Created at: 2024-07-22 01:49:12 UTC  
> Updated at: 2024-09-29 21:12:21 UTC  
> Url: https://github.com/boostorg/parser/issues/182  

Currently my biggest gripe with Spirit.X3 is that you can't parse into a class with private fields. This leaves you with two options:  
  
1. use a proxy type, e.g.  
  
```cpp  
  
class Myclass_impl {  
public:  
    char c;  
};  
  
class Myclass : private Myclass_impl {  
public:  
    Myclass_impl &base() { return *this; }  
};  
  
bp::rule<struct tag, Myclass_impl> rule{"rule"};  
const auto rule_def = bp::char_;  
BOOST_PARSER_DEFINE_RULES(rule);  
  
void func() {  
std::string str{"c"};  
Myclass result;  
  
bp::parse(str, rule, result.base());  
}  
  
```  
  
2. Just don't use private members, which means your parser is no longer a parser, just a validator.  
  
`1.` makes public headers unreadable as users would have to look into implementation headers to see the actual class body, while    
`2.` violates the [parse, don't validate](https://lexi-lambda.github.io/blog/2019/11/05/parse-don-t-validate/) principle and thus allows for invalidly constructed objects that would never be returned from a successful parse.  
  
Are there any plans to remedy this, e.g. via a Boost.Parser helper class that the user class could befriend, or via a specialization that grants access to the private members (Boost.Describe comes to mind) ?  
  
Or am I just missing a workaround that everyone has been using for the past decade

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-09-29 21:10:08 UTC  
> Url: https://github.com/boostorg/parser/issues/182#issuecomment-2381604056  

Uh, no, there are no plans for this.  IMO, no library should be writing into your private data members without the purpose of the library being very closely tied to the domain of your type (stl_interfaces::iterator_interface used as an adaptor, for instance).

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-09-29 21:12:19 UTC  
> Url: https://github.com/boostorg/parser/issues/182#issuecomment-2381604599  

Actually, now that I think about it a bit, this might not be a bad idea once we get P2996 in the standard (hopefully, that's C++26).  At that point, it might make sense to have an access-granting type that you can be`friend`, and then just use the reflection machinery.  I'll leave this ticket open to investigate that.
