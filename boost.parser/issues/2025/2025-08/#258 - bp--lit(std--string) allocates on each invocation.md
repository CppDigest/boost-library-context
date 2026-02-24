# #258 - bp::lit(std::string) allocates on each invocation [Closed]

> Username: andreasbuhr  
> Created at: 2025-08-04 13:42:07 UTC  
> Updated at: 2025-10-13 01:08:27 UTC  
> Closed at: 2025-10-13 01:08:27 UTC  
> Url: https://github.com/boostorg/parser/issues/258  

boost::parser::lit(string) is currently implemented as omit[string_parser]. The string_parser allocates an std::string, which is later discarded by the omit_parser. In release mode, it seems that this is optimized away. But in debug mode, the parser becomes very very slow because of this.  
  
I suggest to add an "omit_string_parser", which is the same thing as the string parser, but has detail::nope as Attribute. This omit_string_parser does not allocate on use.

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-10-13 01:08:27 UTC  
> Url: https://github.com/boostorg/parser/issues/258#issuecomment-3395574066  

This is something that I have to write tests and docs for, as well as maintain.  Increased debug perf doesn't seem worth it to me.
