# #206 - Invalid #pragma parsing [Closed]

> Username: AlexBAV  
> Created at: 2024-01-30 13:32:59 UTC  
> Updated at: 2024-01-30 18:17:58 UTC  
> Closed at: 2024-01-30 13:45:53 UTC  
> Url: https://github.com/boostorg/wave/issues/206  

After #188 the following source code is parsed incorrectly by Boost.Wave:  
  
```c++  
#pragma some(some)  
#include "path"  
```  
  
The line end at the end of pragma line is "eaten" and next token after `right paren` returned is literally `#include "path"`. Adding another newline after the `#pragma` line fixes this.  
  
This started in boost 1.84.0, apparently after #188.

---

## Comment 1

> Username: AlexBAV  
> Created at: 2024-01-30 13:45:53 UTC  
> Url: https://github.com/boostorg/wave/issues/206#issuecomment-1916885965  

Sorry for reporting, duplicate of #200.

---

## Comment 2

> Username: jefftrull  
> Created at: 2024-01-30 18:17:57 UTC  
> Url: https://github.com/boostorg/wave/issues/206#issuecomment-1917629932  

Sorry about the problem, hope you are able to use our develop branch until 1.85 is released.
