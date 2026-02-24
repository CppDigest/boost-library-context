# #178 Fix missing include for std::exception [Merged]

> Username: jschueller  
> Created at: 2024-08-31 07:13:56 UTC  
> Updated at: 2024-09-03 07:39:17 UTC  
> Merged at: 2024-09-03 07:27:59 UTC  
> Closed at: 2024-09-03 07:27:59 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/178  

Fixes build on mingw:   
from_exception.cpp:333:23: error: 'current_exception' is not a member of 'std'  
  
/cc @apolukhin from #147

---

## Comment 1

> Username: coveralls  
> Created_at: 2024-09-03 07:39:16 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/178#issuecomment-2325811760  

[![Coverage Status](https://coveralls.io/builds/69570437/badge)](https://coveralls.io/builds/69570437)  
  
coverage: 86.35%. remained the same  
when pulling **699ed67d6491a36a2f5afe6c021a67634ec96245 on jschueller:patch-1**  
into **ad121dbed069fd01bbc5c1d2b51e801d558ee6ab on boostorg:develop**.

---
