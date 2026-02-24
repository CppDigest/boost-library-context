# #87 fix unused local typedef [Merged]

> Username: tabe  
> Created at: 2015-04-30 09:36:19 UTC  
> Updated at: 2015-04-30 09:54:22 UTC  
> Merged at: 2015-04-30 09:54:22 UTC  
> Closed at: 2015-04-30 09:54:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/87  

g++ 4.9.2 generates -Wunused-local-typedefs as follows;  
  
```  
/home/tabe/isopt/boost_1_58_0/include/boost/spirit/home/lex/lexer/string_token_def.hpp: In member function ‘void boost::spirit::lex::string_token_def<String, IdType, CharEncoding>::collect(LexerDef&, const String_&, const String_&) const’:  
/home/tabe/isopt/boost_1_58_0/include/boost/spirit/home/lex/lexer/string_token_def.hpp:104:48: warning: typedef ‘id_type’ locally defined but not used [-Wunused-local-typedefs]  
             typedef typename LexerDef::id_type id_type;  
                                                ^  
```

---
