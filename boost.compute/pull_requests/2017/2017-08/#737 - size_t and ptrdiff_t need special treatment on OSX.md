# #737 size_t and ptrdiff_t need special treatment on OSX [Merged]

> Username: ddemidov  
> Created at: 2017-08-08 08:25:43 UTC  
> Updated at: 2017-08-10 11:26:06 UTC  
> Merged at: 2017-08-10 11:24:50 UTC  
> Closed at: 2017-08-10 11:24:50 UTC  
> Url: https://github.com/boostorg/compute/pull/737  

The types do not resolve to `ulong` and `long` on OSX, as opposed to other platforms.  
See ddemidov/vexcl#222

---

## Comment 1

> Username: ddemidov  
> Created_at: 2017-08-08 08:29:07 UTC  
> Updated_at: 2017-08-08 08:29:52 UTC  
> Url: https://github.com/boostorg/compute/pull/737#issuecomment-320888423  

Unfortunately, I don't have access to OSX, so I am not able to test this. @chrism0dwk, would you mind copying [compute/types/size_t.hpp](https://github.com/ddemidov/compute/blob/c9522ad4c0f231bbaa626164cd0741f5783a42d7/include/boost/compute/types/size_t.hpp) to your OSX machine and testing if the following  compiles?  
```cpp  
#include <iostream>  
#include <boost/compute.hpp>  
#include "size_t.hpp"  
#define VEXCL_BACKEND_COMPUTE  
#include <vexcl/vexcl.hpp>  
  
int main() { }  
```

---

## Comment 2

> Username: chrism0dwk  
> Created_at: 2017-08-08 08:51:20 UTC  
> Url: https://github.com/boostorg/compute/pull/737#issuecomment-320893616  

@ddemidov yes, your test program compiles on my OSX machine with the size_t.hpp file present on the include path.

---

## Comment 3

> Username: coveralls  
> Created_at: 2017-08-08 09:32:19 UTC  
> Url: https://github.com/boostorg/compute/pull/737#issuecomment-320903723  

[![Coverage Status](https://coveralls.io/builds/12738731/badge)](https://coveralls.io/builds/12738731)  
  
Coverage remained the same at 83.739% when pulling **c9522ad4c0f231bbaa626164cd0741f5783a42d7 on ddemidov:osx-size_t** into **0c2f749fb82dbc482e0d1c77996c010ce2631bb3 on boostorg:develop**.

---

## Comment 4

> Username: ddemidov  
> Created_at: 2017-08-08 10:10:16 UTC  
> Url: https://github.com/boostorg/compute/pull/737#issuecomment-320912871  

Travis fails, but it does not seem related to the PR.

---
