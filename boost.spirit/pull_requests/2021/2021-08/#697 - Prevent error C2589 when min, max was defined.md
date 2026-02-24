# #697 Prevent error C2589 when min, max was defined. [Merged]

> Username: UMU618  
> Created at: 2021-08-21 14:14:07 UTC  
> Updated at: 2021-08-21 16:42:36 UTC  
> Merged at: 2021-08-21 16:42:36 UTC  
> Closed at: 2021-08-21 16:42:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/697  

On Windows/MSVC, the following codes will repro error C2589:  
  
```cpp  
#include <boost/regex.hpp>  
#include <boost/spirit/include/phoenix_bind.hpp>  
#include <boost/spirit/include/phoenix_operator.hpp>  
#include <boost/spirit/include/qi.hpp>  
  
int main() {}  
```

---
