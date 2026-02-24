# #40 Fix spelling of hexadecimal [Merged]

> Username: sehe  
> Created at: 2023-12-30 17:15:07 UTC  
> Updated at: 2023-12-31 00:29:06 UTC  
> Merged at: 2023-12-30 21:13:04 UTC  
> Closed at: 2023-12-30 21:13:04 UTC  
> Url: https://github.com/boostorg/parser/pull/40  

The library and documentation misspells it (consistently, which is good :)) as hexidecimal. That makes it hard for (new) users to find out by searching documentation.  
  
The fifteen spots fixed:  
  
    example/callback_json.cpp|47 col 15| "four hexidecimal digits";  
    example/callback_json.cpp|49 col 18| "\\uXXXX hexidecimal escape sequence";  
    example/callback_json.cpp|51 col 38| escape_double_seq = "\\uXXXX hexidecimal escape sequence";  
    example/json.cpp|57 col 28| // like "expected four hexidecimal digits here:", instead of "expected  
    example/json.cpp|65 col 15| "four hexidecimal digits";  
    example/json.cpp|67 col 18| "\\uXXXX hexidecimal escape sequence";  
    example/json.cpp|69 col 38| escape_double_seq = "\\uXXXX hexidecimal escape sequence";  
    example/json.cpp|158 col 18| // declare a hexidecimal parser that matches exactly 4.  
    doc/tutorial.qbk|981 col 18| [ Matches a hexidecimal unsigned integral value. ]  
    doc/tutorial.qbk|986 col 28| [ Matches exactly the hexidecimal unsigned integral value `_RES_np_(arg0)`. ]  
    doc/tutorial.qbk|1118 col 42| So, if you wanted to parse exactly eight hexidecimal digits in a row in order  
    doc/tutorial.qbk|2729 col 18| "\\uXXXX hexidecimal escape sequence";  
    doc/tutorial.qbk|2731 col 38| escape_double_seq = "\\uXXXX hexidecimal escape sequence";  
    doc/tutorial.qbk|2741 col 9| put a hexidecimal escape sequence there.  
    include/boost/parser/parser.hpp|5622 col 13| /** The hexidecimal unsigned integer parser.  Produces an `unsigned int`  
  
The one spot that was inconsistent (outside the gtest subtree):  
  
    include/boost/parser/tuple.hpp|66 col 36| // 0xDEADBEEF (hexadecimal)

---

## Comment 1

> Username: tzlaine  
> Created_at: 2023-12-30 21:13:02 UTC  
> Url: https://github.com/boostorg/parser/pull/40#issuecomment-1872608294  

Thanks!

---
