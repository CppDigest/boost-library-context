# #393 Fix scanner regex pattern [Closed]

> Username: jonathanpoelen  
> Created at: 2019-02-18 23:59:54 UTC  
> Updated at: 2021-10-02 21:19:14 UTC  
> Closed at: 2019-03-01 00:10:21 UTC  
> Url: https://github.com/boostorg/build/pull/393  

Before, commented includes are added as dependencies  
  
```cpp  
#include "a.h" // recompiled if a.h is updated: ok  
// #include "b.h" // recompiled if b.h is updated: bad  
int main() {}  
```  
  
The new pattern checks that `#` is preceded by 0 or more blank characters

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2019-02-19 00:12:06 UTC  
> Url: https://github.com/boostorg/build/pull/393#issuecomment-464928681  

The current behavior is intentional. It is used to allow for detecting include dependencies where the included header is a macro. I.e.:  
```  
#define SOME_HEADER "qwerty.h"  
// #include "qwerty.h"  
#include SOME_HEADER  
```  
Obviously that is a simplified example. Maybe @swatanabe can say more.

---

## Comment 2

> Username: jonathanpoelen  
> Created_at: 2019-02-22 17:12:16 UTC  
> Url: https://github.com/boostorg/build/pull/393#issuecomment-466472460  

I am moderately convinced by this approach. In fact, why use a regex rather than a compiler mechanisms that lists includes?

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2019-02-22 17:22:39 UTC  
> Url: https://github.com/boostorg/build/pull/393#issuecomment-466475925  

We do intend at some point to support the post-compile-preprocessor-scan model. But we will always need regex header scanning as not all compilers support that mode and not all builds have the concept (for example doc tools).

---
