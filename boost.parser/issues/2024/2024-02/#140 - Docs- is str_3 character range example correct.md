# #140 - Docs: is str_3 character range example correct? [Closed]

> Username: LegalizeAdulthood  
> Created at: 2024-02-28 18:22:19 UTC  
> Updated at: 2024-02-28 23:47:42 UTC  
> Closed at: 2024-02-28 23:47:42 UTC  
> Url: https://github.com/boostorg/parser/issues/140  

On the page [The parse() API](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/the__parse____api.html), the code snippet in the second code block is given as:  
```  
char const * str_3 = "str";  
auto result_3 = bp::parse(str_3 | boost::parser::as_utf16, p, bp::ws);  
```  
This is saying to parse the range of `char` characters as UTF-16?  Should this be `char16_t`?

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-28 23:47:42 UTC  
> Url: https://github.com/boostorg/parser/issues/140#issuecomment-1970107889  

No, the example is correct.  `str_3 | boost::parser::as_utf16` is a transcoding view that produces `char16_t`s.
