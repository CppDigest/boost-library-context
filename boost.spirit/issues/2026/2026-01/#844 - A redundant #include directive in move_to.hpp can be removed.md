# #844 - A redundant #include directive in move_to.hpp can be removed [Closed]

> Username: ILer32  
> Created at: 2026-01-15 15:16:29 UTC  
> Updated at: 2026-01-16 01:21:31 UTC  
> Closed at: 2026-01-16 01:21:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/844  

Problem:  
In [spirit/include/boost/spirit/home/x3/support/traits/move_to.hpp](https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/support/traits/move_to.hpp),   
one #include <boost/fusion/include/is_sequence.hpp> can be removed.  
```  
....  
#include <boost/assert.hpp>  
#include <boost/fusion/include/is_sequence.hpp> // 1  
#include <boost/fusion/include/front.hpp>  
#include <boost/fusion/include/move.hpp>  
#include <boost/fusion/include/is_sequence.hpp> // 2  
#include <utility>  
```

---

## Comment 1

> Username: djowel  
> Created at: 2026-01-16 00:37:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/844#issuecomment-3757530691  

Will you kindly file a PR for this? Thanks!

---

## Comment 2

> Username: ILer32  
> Created at: 2026-01-16 01:21:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/844#issuecomment-3757635845  

The issue is solved.
