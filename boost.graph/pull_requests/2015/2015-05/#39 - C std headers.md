# #39 C std headers [Merged]

> Username: atombrella  
> Created at: 2015-05-26 15:57:07 UTC  
> Updated at: 2016-04-24 16:26:25 UTC  
> Merged at: 2016-04-24 16:26:25 UTC  
> Closed at: 2016-04-24 16:26:25 UTC  
> Url: https://github.com/boostorg/graph/pull/39  

The function in 17e0154 is not compiled (the C++11 dictates that this signature is valid standard C++, but probably wasn't standard in 2003 when the comment was written.  
  
I replaced a number of imports so they match the style in the C++ standard, e.g., <stdlib.h> => <cstdlib>, and since my editor deletes trailing whitespace, affected files, I also cleaned this up.   
  
include/boost/pending/fibonacci_heap.hpp has this snippet, but I'm uncertain whether <math.h> can also be changed into <cmath>  
  
```  
#if defined(__sgi) && !defined(__GNUC__)  
# include <math.h>  
#else  
```

---

## Comment 1

> Username: atombrella  
> Created_at: 2015-06-15 11:18:43 UTC  
> Url: https://github.com/boostorg/graph/pull/39#issuecomment-112022722  

@Belcourt could you please have a look at this?

---

## Comment 2

> Username: atombrella  
> Created_at: 2015-07-16 15:08:00 UTC  
> Url: https://github.com/boostorg/graph/pull/39#issuecomment-121985406  

@ericniebler because of the upcoming 1.59, can somebody please have a look at all these pull requests?

---

## Comment 3

> Username: ericniebler  
> Created_at: 2015-07-16 15:23:04 UTC  
> Url: https://github.com/boostorg/graph/pull/39#issuecomment-121991352  

Send a message to the Boost list and glad the Community Maintenance Team. I am not the maintainer of the BGL.

---
