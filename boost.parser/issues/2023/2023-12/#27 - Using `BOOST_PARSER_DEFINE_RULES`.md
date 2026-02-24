# #27 - Using `BOOST_PARSER_DEFINE_RULES` [Closed]

> Username: akrzemi1  
> Created at: 2023-12-29 11:21:11 UTC  
> Updated at: 2023-12-30 12:48:20 UTC  
> Closed at: 2023-12-30 01:54:30 UTC  
> Url: https://github.com/boostorg/parser/issues/27  

The docs and examples, obviously, use `BOOST_PARSER_DEFINE_RULES`. But it is not easy to find the description of what it does from the docs. I would expect to find it in the reference secion, but the reference page doesn't list it. Nor is it easy to figure out which header it is defined in.  
  
Second, the examples suggest that in order for macro invocation `BOOST_PARSER_DEFINE_RULES(xxx)` to work, I need to define `xxx_def` beforehand. I find it uncomfortable that the macro invocation depends on the name not listed in macro arguments. This seems to go against the referential transparency expectations. Any such `xxx_def` looks like an unused variable.

---

## Comment 1

> Username: tzlaine  
> Created at: 2023-12-29 19:37:52 UTC  
> Url: https://github.com/boostorg/parser/issues/27#issuecomment-1872300741  

> The docs and examples, obviously, use BOOST_PARSER_DEFINE_RULES. But it is not easy to find the description of what it does from the docs. I would expect to find it in the reference secion, but the reference page doesn't list it. Nor is it easy to figure out which header it is defined in.  
  
It's in the parser.hpp header reference.  I don't have a separate page for macros; there are only two. :)  I'll add explicit links from the tutorial page on rules to `BOOST_PARSER_DEFINE_RULE*()`.  
  
> Second, the examples suggest that in order for macro invocation BOOST_PARSER_DEFINE_RULES(xxx) to work, I need to define xxx_def beforehand. I find it uncomfortable that the macro invocation depends on the name not listed in macro arguments. This seems to go against the referential transparency expectations. Any such xxx_def looks like an unused variable.  
  
I don't think I know how to change this in a way that will look better to you.  The magic that makes rules work is the macro, plus the naming convention.  Everyone who uses this lib more than once will understand what the `_def` things are; they're just a convention you have to know.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2023-12-29 20:26:55 UTC  
> Url: https://github.com/boostorg/parser/issues/27#issuecomment-1872322419  

I may be talking nonsense now, as I do not know the library well enough, but it looks like you need to associate two things with a rule: the name and the parser. Would the following interface not do?  
  
```c++  
bp::rule<struct doubles, std::vector<double>> doubles = {  
  "doubles",   
  bp::double_ >> *(',' >> bp::double_)  
};  
```  
  
  
  
Alternatively, the macro could take two parameters:   
  
```c++  
BOOST_PARSER_DEFINE_RULE(doubles, doubles_ref);  
```  
  
Which is more verbose, but has fewer surprises.

---

## Comment 3

> Username: tzlaine  
> Created at: 2023-12-30 01:54:31 UTC  
> Url: https://github.com/boostorg/parser/issues/27#issuecomment-1872424479  

Sure, the first way of associating rules might work, but it closes the door on separate compilation.  
  
As for repeating the rule name in `BOOST_PARSER_DEFINE_RULE*`, we could do that, but why?  Repetition leads to copy/paste bugs.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2023-12-30 12:11:58 UTC  
> Url: https://github.com/boostorg/parser/issues/27#issuecomment-1872516436  

> Sure, the first way of associating rules might work, but it closes the door on separate compilation.  
  
Oh. That is  use case worth describing in the docs.  
   
> As for repeating the rule name in `BOOST_PARSER_DEFINE_RULE*`, we could do that, but why? Repetition leads to copy/paste bugs.  
  
That's right. On the other hand, you see it as repetition only because you already enforce the pattern `xxx`-`xxx_def`. Users would be able to provide different names. Conceptually you need two things to build the rule: The type representing the rule and the parser. So passing two arguments seems to reflect the intent.

---

## Comment 5

> Username: akrzemi1  
> Created at: 2023-12-30 12:48:19 UTC  
> Url: https://github.com/boostorg/parser/issues/27#issuecomment-1872521902  

Also, if the argument for the `_def` is the "don't repeat yourself", how can one justify this recommended practice:  
  
```c++  
bp::rule<struct doubles, std::vector<double>> doubles = "doubles";  
```  
  
where the name "doubles" occurs three times?
