# #111 - Add a type trait for getting `ATTR(p)` [Closed]

> Username: tzlaine  
> Created at: 2024-02-23 01:29:45 UTC  
> Updated at: 2024-03-15 21:29:28 UTC  
> Closed at: 2024-03-15 21:29:28 UTC  
> Url: https://github.com/boostorg/parser/issues/111  

From me in the Boost review:  
  
> This seems reasonable to me.  This would be something like, "attr_t<R,  
> P>" where R is the range to parse, and P is the parser.  The range is  
> needed because the attribute type of char_ differs in the Unicode- and  
> non-Unicode code paths (see  
> https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/rationale.html#boost_parser__proposed_.rationale._globalname_alt__boost__parser__char____code__phrase_role__identifier__char___phrase___code___globalname__s_attribute_type_is_polymorphic  
).

---

## Comment 1

> Username: akrzemi1  
> Created at: 2024-02-23 20:46:06 UTC  
> Url: https://github.com/boostorg/parser/issues/111#issuecomment-1961967900  

Going a bit further, would it be possible to add additional concept constraint to the declaration of `parse()`:  
  
```c++  
  template<  
      parsable_range_like R,  
      typename Parser,  
      typename GlobalState,  
      typename ErrorHandler,  
      attr_compatible_with<R, Parser> Attr> // <-- new concept  
  bool parse(  
      R const & r,  
      parser_interface<Parser, GlobalState, ErrorHandler> const & parser,  
      Attr & attr,  
      trace trace_mode = trace::off);  
```

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-02-24 00:54:19 UTC  
> Url: https://github.com/boostorg/parser/issues/111#issuecomment-1962185662  

This is not a useful change; we discussed this already in #77.  I think all the same logic applies here.  In short, doing all the checking up front simply cannot be done, due to the fact that there are multiple ways that the result could get assigned, and only some of those are reflected in the type system.  A notable one that is not is assignment in a semantic action!

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-02-24 08:32:33 UTC  
> Url: https://github.com/boostorg/parser/issues/111#issuecomment-1962298379  

It may not be implementable, but it is certinly useful.   
I think https://github.com/tzlaine/parser/issues/77#issuecomment-1911262439 applies to determinimg the properties of a parser in the context of defining a rule. Whereas my present suggestion is in the context of invoking function `parse()`. This is different because, as I can see, in this context you are not allowed to assign semantic actions to a parser:  
  
```c++  
std::string input = "3";  
std::string output;  
  
auto act = [](auto ctx){};  
auto str = bp::int_[act];  
auto b = bp::parse(input, str, bp::ws, output); // static_assert fires  
```  
https://godbolt.org/z/6fbjGfnof    
  
So it seems to me that the compatibility of the attribute passed to a parse can be determined by three types:  
* type of the attribute passed to `parse()`  
* Character type of `R` (text to parse)  
* type of the parser

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-02-24 21:00:17 UTC  
> Url: https://github.com/boostorg/parser/issues/111#issuecomment-1962730366  

The parser produces no attribute at all.  The no-attribute case is special.  So, we can provide the type trait equivalent to `ATTR(p)`, and the user can see if it produces an attribute or not.  However, constraining the `*parse()` functions with a constraint based on the trait is still a bad idea.  
  
The only exception might be passing an out-param when there's no attribute, but in the general case it's still not useful.  I don't think this special case will come up very often, or be very hard to diagnose when it does, so I'm disinclined to add the special-case check.
