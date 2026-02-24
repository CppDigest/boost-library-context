# #115 - GCC -Wimplicit-fallthrough warnings and misspelling [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-11 17:59:12 UTC  
> Updated at: 2020-04-13 21:35:52 UTC  
> Closed at: 2020-04-13 21:31:28 UTC  
> Url: https://github.com/boostorg/log/issues/115  

I built Boost 1.72 with GCC 7.5 with added -Wextra in cxxflags. Problems are still in current code:  
  
libs/log/src/code_conversion.cpp:122:17: warning: this statement may fall through [-Wimplicit-fallthrough=]  
libs/log/src/named_scope_format_parser.cpp:117:9: warning: this statement may fall through [-Wimplicit-fallthrough=]  
libs/log/src/named_scope_format_parser.cpp:295:17: warning: this statement may fall through [-Wimplicit-fallthrough=]  
  
Boost has `BOOST_FALLTHROUGH` for this purpose.  
  
Misspelling: libs/log/src/named_scope_format_parser.cpp:132 opeartor  
libs/log/src/named_scope_format_parser.cpp:358:17: warning: this statement may fall through [-Wimplicit-fallthrough=]

---

## Comment 1

> Username: EugeneZelenko  
> Created at: 2020-04-13 21:35:51 UTC  
> Url: https://github.com/boostorg/log/issues/115#issuecomment-613109815  

Thank you for help!
