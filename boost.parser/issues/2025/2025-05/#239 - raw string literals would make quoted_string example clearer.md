# #239 - raw string literals would make quoted_string example clearer [Closed]

> Username: LegalizeAdulthood  
> Created at: 2025-05-27 04:28:27 UTC  
> Updated at: 2025-07-12 21:10:57 UTC  
> Closed at: 2025-07-12 21:10:57 UTC  
> Url: https://github.com/boostorg/parser/issues/239  

https://github.com/boostorg/parser/blob/5788fb6967fb39db76bc98b415b3fa7776a9ca5e/test/parser_quoted_string.cpp#L327  
  
instead of  
```  
"\"some \\\"text\\\"\""  
```  
use  
```  
R"text("some \"text\"")text"  
```  
then the backslashes intended for the parser are clear instead of doubling up all the backslashes for C++ string quoting.

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-07-12 21:09:50 UTC  
> Url: https://github.com/boostorg/parser/issues/239#issuecomment-3066046600  

Good call.  Done.
