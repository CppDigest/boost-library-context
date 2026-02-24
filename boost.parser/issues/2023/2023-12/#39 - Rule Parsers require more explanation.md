# #39 - Rule Parsers require more explanation [Closed]

> Username: akrzemi1  
> Created at: 2023-12-30 15:31:14 UTC  
> Updated at: 2024-01-10 05:10:16 UTC  
> Closed at: 2024-01-10 05:10:16 UTC  
> Url: https://github.com/boostorg/parser/issues/39  

After reading section [Rule Parsers](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/rule_parsers.html), but without being familiar with Boost.Spirit, one doesn't really get the picture *why* one would want to create such rules, rather than using parsers directly.  
  
I guess that the motivation is to store them as members in classes, but that is more of a guess. I remember that in Boost.Spirit this was a workaround for not being able to use `auto` type deduction for class members. Is this the case also for Boost.Parser? If so, maybe an example with a parser as a class member would be useful?  
  
There is a a note at the end of the section:  
  
> The existence of [`rules`](https://tzlaine.github.io/parser/doc/html/boost/parser/rule.html) means that will probably never have to write a low-level parser. You can just put existing parsers together into [`rules`](https://tzlaine.github.io/parser/doc/html/boost/parser/rule.html) instead.   
  
The computer font implies that `rules` is an identifier defined in the library, but there is no `rules` defined. Only `rule`.  
  
I also do not understand what it means. I can also just put the existing parsers together without using a `rule`:  
  
```c++  
auto doubles = bp::double_ >> *(',' >> bp::double_);  
```  
  
Also, is it possible to have two rules that reuse the same parser?  
  
Finally, the mechanism for defining rules promotes the usage of [elaborated type specifier](https://en.cppreference.com/w/cpp/language/elaborated_type_specifier). This works fine in 98% of the cases, but might cause surprising errors in the remaining 2% as showed in [this lightning talk](https://www.youtube.com/watch?v=26-7V8M1TmQ&list=PLkqnZ61Xh8k7piQktBdkXFTGHoyaW0mRI&index=7&ab_channel=CppNow).

---

## Comment 1

> Username: akrzemi1  
> Created at: 2023-12-30 17:02:12 UTC  
> Url: https://github.com/boostorg/parser/issues/39#issuecomment-1872565520  

Also, the same page mentions that macro `BOOST_PARSER_DEFINE_RULE` "expands to two overloads of a function called `parse_rule()`". This is the first time that the reader is exposed to the name `parse_rule()` and it is not clear how relevant it is to the reader, and that these are two overloads.  
  
Insted, the important information, I think, is that the new rule is also itself a _parser_.

---

## Comment 2

> Username: tzlaine  
> Created at: 2023-12-30 21:33:02 UTC  
> Url: https://github.com/boostorg/parser/issues/39#issuecomment-1872610997  

Yes, these things definitely need amplification.  I find it particularly difficult to document things that I've internalized so much that I "just know" about them.  So, keep these kinds of issues coming; I appreciate them.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2023-12-31 15:47:17 UTC  
> Url: https://github.com/boostorg/parser/issues/39#issuecomment-1872979527  

Or are they needed when parsers refer to one another recursively?
