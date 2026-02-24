# #81 - excessive includes in isotropy.hpp [Open]

> Username: maliberty  
> Created at: 2022-07-26 00:05:09 UTC  
> Updated at: 2022-07-26 21:27:16 UTC  
> Url: https://github.com/boostorg/polygon/issues/81  

Why are there so many includes in isotropy.hpp that aren't required by this header:  
```  
//external  
#include <cmath>  
#include <cstddef>  
#include <cstdlib>  
#include <vector>  
#include <deque>  
#include <map>  
#include <set>  
#include <list>  
//#include <iostream>  
#include <algorithm>  
#include <limits>  
#include <iterator>  
#include <string>  
```  
  
I just want the types defined here without getting 55k lines of preprocessed headers.
