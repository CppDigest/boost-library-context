# #290 fix optional quoted strings [Closed]

> Username: maleiter  
> Created at: 2025-11-12 15:26:52 UTC  
> Updated at: 2025-11-15 22:42:15 UTC  
> Closed at: 2025-11-15 22:42:14 UTC  
> Url: https://github.com/boostorg/parser/pull/290  

Optional quoted string parsers are currently not possible because the result type std::optional<std::string> cannot be handled as a container.   
  
This was fixed by appending the result to an std::string first, then moving it into the std::optional<std::string>.   
   
Example for reproduction of the bug:  
`auto const pTest = (bp::lit("TEST:") > -bp::quoted_string`  
  
A possible workaround:   
`auto const pTest = (bp::lit("TEST:") > (bp::quoted_string | bp::eps)`

---

## Comment 1

> Username: tzlaine  
> Created_at: 2025-11-15 22:42:14 UTC  
> Url: https://github.com/boostorg/parser/pull/290#issuecomment-3537009684  

Thanks for reporting this!  I fixed it a slightly different way, so closing this.

---
