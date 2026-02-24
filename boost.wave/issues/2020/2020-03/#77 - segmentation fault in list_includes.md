# #77 - segmentation fault in list_includes [Closed]

> Username: jefftrull  
> Created at: 2020-03-08 20:54:20 UTC  
> Updated at: 2020-03-13 19:24:49 UTC  
> Closed at: 2020-03-13 19:24:49 UTC  
> Url: https://github.com/boostorg/wave/issues/77  

For some time (I went as far back as 1.41) list_includes gives a segfault on simple input, e.g.:  
  
```c++  
#include "foo.h"  
  
int main() {}  
```
