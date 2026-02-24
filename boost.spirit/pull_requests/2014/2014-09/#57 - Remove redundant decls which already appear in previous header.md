# #57 Remove redundant decls which already appear in previous header. [Merged]

> Username: mlang  
> Created at: 2014-09-13 09:22:31 UTC  
> Updated at: 2014-11-21 17:23:23 UTC  
> Merged at: 2014-11-21 17:23:23 UTC  
> Closed at: 2014-11-21 17:23:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/57  

{expression,statement}_{class,type} are already declared in {expression,statement}.hpp.  
No need to repeat ourselves in {expression,statement}_def.hpp.

---
