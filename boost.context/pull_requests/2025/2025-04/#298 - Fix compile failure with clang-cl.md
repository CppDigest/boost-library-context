# #298 Fix compile failure with clang-cl [Closed]

> Username: niansa  
> Created at: 2025-04-12 01:34:40 UTC  
> Updated at: 2025-04-14 05:45:24 UTC  
> Closed at: 2025-04-14 05:45:23 UTC  
> Url: https://github.com/boostorg/context/pull/298  

clang-cl uses `masm` which does not understand GNU compile options.   
  
```  
Microsoft (R) Macro Assembler (x64) Version 14.43.34810.0  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
MASM : fatal error A1013:invalid numerical command-line argument : /W  
```  
  
This change prevents that from happening.

---

## Comment 1

> Username: marakew  
> Created_at: 2025-04-12 19:57:06 UTC  
> Updated_at: 2025-04-12 19:58:00 UTC  
> Url: https://github.com/boostorg/context/pull/298#issuecomment-2799018669  

the issue more complex  
this must be but still wait  
https://github.com/boostorg/context/pull/294

---

## Comment 2

> Username: niansa  
> Created_at: 2025-04-14 05:45:23 UTC  
> Url: https://github.com/boostorg/context/pull/298#issuecomment-2800535245  

Closing my PR then, the other one looks superior.

---
