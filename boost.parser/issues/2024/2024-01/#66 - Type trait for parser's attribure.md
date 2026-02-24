# #66 - Type trait for parser's attribure [Closed]

> Username: akrzemi1  
> Created at: 2024-01-15 17:43:18 UTC  
> Updated at: 2024-01-15 22:23:58 UTC  
> Closed at: 2024-01-15 22:23:57 UTC  
> Url: https://github.com/boostorg/parser/issues/66  

Docs section Terminology has: "Since it is not possible to write a type trait that returns the attribute type of a parser, we need notation for concisely communicating". But it isnot at all clear why that would be impossible.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2024-01-15 20:37:22 UTC  
> Url: https://github.com/boostorg/parser/issues/66#issuecomment-1892739978  

A sample definition:  
  
```c++  
template <typename Parser>  
using attribute_type = decltype(boost::parser::parse(std::string_view{}, std::declval<Parser>()))::value_type;  
  
```

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-01-15 22:23:57 UTC  
> Url: https://github.com/boostorg/parser/issues/66#issuecomment-1892827455  

`char_`'s attribute type depends on the input.  So that definition is correct as long as the input is always a sequence of `char`.  But it's possibly (but not always, depending on the exact combination of parsers) wrong if the input is a sequence of `char32_t`.  
  
See the rationale section for why I think `char_` polymorphism is an important feature.  
  
However, it's really easy to interrogate this yourself at the point of use: `decltype(bp::parser(str, p)`.
