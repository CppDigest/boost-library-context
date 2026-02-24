# #96 - Request: a better example in `replace` documentation [Closed]

> Username: akrzemi1  
> Created at: 2024-02-19 22:21:26 UTC  
> Updated at: 2024-03-15 21:29:25 UTC  
> Closed at: 2024-03-15 21:29:25 UTC  
> Url: https://github.com/boostorg/parser/issues/96  

Can wee see a motivating use case for `replace`?  
  
The description in the docs:  
https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/algorithms_and_views_that_use_parsers.html#boost_parser__proposed_.tutorial.algorithms_and_views_that_use_parsers._globalname_alt__boost__parser__replace___code__phrase_role__identifier__boost__phrase__phrase_role__special______phrase__phrase_role__identifier__parser__phrase__phrase_role__special______phrase__phrase_role__identifier__replace__phrase___code___globalname_  
  
Doesn't give a hint why anyone would need this feature for.  
  
The example with `bp::lit("XYZ")` isn't very convincing. It looks like `replace` for strings would be better.  
  
Is there a real life use case for this that we could see?

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-19 22:27:26 UTC  
> Url: https://github.com/boostorg/parser/issues/96#issuecomment-1953229641  

You really want me to justify the utility of string replacement?

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-02-20 00:20:54 UTC  
> Url: https://github.com/boostorg/parser/issues/96#issuecomment-1953306290  

The docs need to give an example anyway. I think demonstrating a parser that is a `bp::lit` is not the best choice: there are simpler ways to replace one string literal with another.   
Maybe instead an example that erases the card numbers:  
  
```c++  
auto card_number = bp::repeat(4)[bp::int_] >> bp::repeat(3)['-' >> bp::repeat(4)[bp::int_]];  
auto card_number_wipe = bp::replace(card_number , "XXXX-XXXX-XXXX-XXXX");  
```

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-02-20 00:35:14 UTC  
> Url: https://github.com/boostorg/parser/issues/96#issuecomment-1953315254  

Ok, I see.  Yeah, that's a much better example.  I'll change this.
