# #92 - Add more description of alternative parser [Closed]

> Username: akrzemi1  
> Created at: 2024-02-03 17:03:46 UTC  
> Updated at: 2024-02-04 22:36:56 UTC  
> Closed at: 2024-02-04 22:36:56 UTC  
> Url: https://github.com/boostorg/parser/issues/92  

[Alternative Parsers](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/alternative_parsers.html) discusses the interaction between `eps` and the alternative parser, but the surprising behavior we get in any case where the former alternative is a prefix of the following alternative. For instance the following is certainly a progrmmer bug: `int_ | (int_ >> int_)`.  
  
https://godbolt.org/z/rfhKxaKPP   
  
This should be documented in the docs also. Maybe there is a way to statically detect it too?
