# #595 - simple example of matching a unicode charater, and range [Open]

> Username: steven-varga  
> Created at: 2020-05-04 14:09:07 UTC  
> Updated at: 2025-05-10 00:40:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/595  

Is it possible to match a unicode character `char_(0x00b7)`  
I am trying to write a parser for [RDF Quads](https://www.w3.org/TR/n-quads/#n-quads-language) and measure the performance of `boost::spirit` an a published massive dataset. In the production rules:  
```  
PN_CHARS ::= PN_CHARS_U | '-' | [0-9] | #x00B7 | [#x0300-#x036F] | [#x203F-#x2040]  
```  
where `#x00B7` is a two byte character. What is a good strategy? How can I make this happen?  
  
Similarly, how to specify character sets containing unicode?  
```  
IRIREF | ::= | '<' ([^#x00-#x20<>"{}\|^`] | UCHAR)* '>'  
```  
Perhaps I missed something in the documentation, example or discussion, if so a link/pointer could be a great start. Thank you in advance for any hints,   
steve
