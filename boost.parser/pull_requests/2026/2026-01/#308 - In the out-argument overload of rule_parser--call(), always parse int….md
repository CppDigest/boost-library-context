# #308 In the out-argument overload of rule_parser::call(), always parse int… [Merged]

> Username: tzlaine  
> Created at: 2026-01-29 07:33:32 UTC  
> Updated at: 2026-01-31 01:16:34 UTC  
> Merged at: 2026-01-31 01:16:34 UTC  
> Closed at: 2026-01-31 01:16:34 UTC  
> Url: https://github.com/boostorg/parser/pull/308  

…o a local  
  
attr_type attribute ('attr'), rather than the one supplied ('retval').  Then, take the parsed attribute and either combine it with retval (if they are both containers), or assign attr to retval (if attr_type is not none).  
  
Reproducer by Andreas Buhr.  
  
Fixes #294.

---
