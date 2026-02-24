# #107 - [review] consider removing reference collapsing logic [Closed]

> Username: badair  
> Created at: 2017-04-09 03:28:16 UTC  
> Updated at: 2017-11-04 20:45:17 UTC  
> Closed at: 2017-11-04 20:45:16 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/107  

> "reference collapsing" behavior on the add ref-qualifier traits is subtle and arbitrary and I'm not aware of use cases calling for such rules. The FAQ's argument - that a different set of rules would be  
hard to memorize - does not sound very convincing to me. It sounds like the problem came from the name of the trait: "adding" a && qualifier to a &-qualified function type doesn't make much sense. If,  
say, the name is recast as "make the function type && qualified", then the behavior would be obvious.   
  
from http://lists.boost.org/Archives/boost/2017/04/234173.php

---

## Comment 1

> Username: badair  
> Created at: 2017-11-04 20:45:16 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/107#issuecomment-341928578  

Good suggestion, but closing as won't fix.
