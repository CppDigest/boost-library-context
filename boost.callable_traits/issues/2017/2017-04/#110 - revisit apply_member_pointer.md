# #110 - [review] revisit apply_member_pointer [Open]

> Username: badair  
> Created at: 2017-04-09 03:32:49 UTC  
> Updated at: 2017-04-13 03:03:30 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/110  

> apply_member_pointer seems to be trying to do too much. I can get a  
pointer to member data of any type, unless that data member is itself  
a pointer to member or a pointer to function, in which case I get  
something else entirely. Additionally, the description doesn't mention  
that PMDs also get special handling.  
  
from http://lists.boost.org/Archives/boost/2017/04/234173.php

---

## Comment 1

> Username: badair  
> Created at: 2017-04-09 03:33:04 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/110#issuecomment-292761385  

Is it useful at all? Should it be split into multiple traits, or removed entirely? What was the rationale for the current design?
