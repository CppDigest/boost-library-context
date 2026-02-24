# #71 - Shorter way of defining a rule? [Closed]

> Username: akrzemi1  
> Created at: 2024-01-17 22:58:42 UTC  
> Updated at: 2024-01-20 03:08:40 UTC  
> Closed at: 2024-01-20 03:08:40 UTC  
> Url: https://github.com/boostorg/parser/issues/71  

Is there any implementation difficulty in changing macro `BOOST_PARSER_DEFINE_RULE` to accept two parameters: rule and the parser?  
  
```c++  
bp::rule<struct doubles, std::vector<double>> doubles = "doubles";  
BOOST_PARSER_DEFINE_RULE(doubles, bp::double_ % ',');  
```  
Because this is definately shorter. And the longer variant would still be available with the plural form:  
  
```c++  
bp::rule<struct doubles, std::vector<double>> doubles = "doubles";  
auto const doubles_def = bp::double_ % ',';  
BOOST_PARSER_DEFINE_RULES(doubles);  
```

---

## Comment 1

> Username: akrzemi1  
> Created at: 2024-01-17 23:05:04 UTC  
> Url: https://github.com/boostorg/parser/issues/71#issuecomment-1897329742  

BTW, section [More About Rules](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/more_about_rules.html) says, "The reason for this is that, inside the rule parsing implementation, there is code something like this".  
  
It may be just a bad choice of words, but it sounds like the implementation is driving the interface, as if you were saying, "this is how rules work because I couldn't implement it differently."

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-01-18 00:44:39 UTC  
> Url: https://github.com/boostorg/parser/issues/71#issuecomment-1897564674  

That seems reasonable.  I like the idea of reducing the repetition of the rule name.  This is pretty low-priority, though.  
  
> BTW, section [More About Rules](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/more_about_rules.html) says, "The reason for this is that, inside the rule parsing implementation, there is code something like this".  
>   
> It may be just a bad choice of words, but it sounds like the implementation is driving the interface, as if you were saying, "this is how rules work because I couldn't implement it differently."  
  
That's the way the entire library works internally.  It's not so much a limitation on the implementation as a conscious choice to organize things a certain way, with resulting sematics.  The example wasn't to explain "this is why you can't have nice things".  It was intended to give you a little example that would be easy to remember (with C++ programmers I find that that usually means code) for why the code in the example does what it does.

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-01-20 03:08:40 UTC  
> Url: https://github.com/boostorg/parser/issues/71#issuecomment-1901700423  

I just wrote this out, and it's:  
  
```c++  
#define BOOST_PARSER_DEFINE_RULE_DEF(rule, parser_expr)                        \  
    constexpr auto rule##_def = parser_expr  
```  
  
People should just write that out.  The macro is not really making like any easier.  So, I'm not going to do this one.  However, I am going to get rid of `BOOST_PARSER_DEFINE_RULE`, which is superfluous now that we always have `BOOST_PARSER_DEFINE_RULES`.  The only reaons for the singular form was that the plural form was previously optionally defined.
