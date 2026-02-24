# #49 - missing header in extended_p_square_quantile.hpp [Open]

> Username: jefftrull  
> Created at: 2021-06-21 00:05:09 UTC  
> Updated at: 2021-06-21 00:05:09 UTC  
> Url: https://github.com/boostorg/accumulators/issues/49  

This header uses `std::ostringstream` without first including `<sstream>`, resulting in compile errors.  
Minimal reproducer:  
  
```c++  
#include <boost/accumulators/statistics/extended_p_square_quantile.hpp>  
  
int main() {}  
```
