# #741 - iso8859_1::isgraph doesn't work correctly [Open]

> Username: WitekT  
> Created at: 2022-11-15 11:43:13 UTC  
> Updated at: 2025-05-09 23:25:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/741  

I parse such string: "¶" (0xb6 character) with such parser:  
```cpp  
boost::spirit::qi::rule< std::string::const_iterator > validCharRule  
	= +( boost::spirit::qi::iso8859_1::graph );  
  
std::string test = "\xb6";  
auto it = test.cbegin();  
auto end = test.cend();  
  
if ( !boost::spirit::qi::parse( it, end, validCharRule ) || it != end )  
	std::cout << "error: " << std::string( it, end ) << std::endl;  
else  
	std::cout << "ok" << std::endl;  
```  
The problem is here, but may be also cause by cast_char from BOOST_SPIRIT_CLASSIFY:  
```cpp  
isgraph(int ch)  
        {  
            return ('\x21' <= ch && ch <= '\x7e') || ('\xa1' <= ch && ch <= '\xff');  
        }  
```  
ch is equal to 182 and it is compared eg. with -95 ('\xa1') -1 ('\xff').
