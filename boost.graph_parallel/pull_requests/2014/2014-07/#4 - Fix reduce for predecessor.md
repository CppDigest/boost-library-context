# #4 Fix reduce for predecessor. [Merged]

> Username: marcinz  
> Created at: 2014-07-13 03:07:10 UTC  
> Updated at: 2014-07-13 22:15:16 UTC  
> Merged at: 2014-07-13 22:15:16 UTC  
> Closed at: 2014-07-13 22:15:16 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/4  

The assumption was that the predecessor, which is a distributed vertex descriptor, should be  
the same as the key. The key is actually a local vertex descriptor, and these two types may  
not match.  
  
Signed-off-by: Marcin Zalewski marcin.zalewski@gmail.com

---

## Comment 1

> Username: Belcourt  
> Created_at: 2014-07-13 17:38:05 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/4#issuecomment-48846934  

I'm unclear why this is needed, can you explain a bit more?  Specifically, consider the case where the distributed vertex descriptor type is different from the local vertex descriptor (e.g. signed and unsigned ints). Does this patch fix a compile or runtime issue, or are you just clarifying the fact the Key must be convertible to T and changing the function call operator makes that clearer?

---

## Comment 2

> Username: marcinz  
> Created_at: 2014-07-13 21:14:02 UTC  
> Url: https://github.com/boostorg/graph_parallel/pull/4#issuecomment-48852098  

Here is the discussion of the problem (check my answer to the original report):  
  
http://lists.boost.org/boost-users/2014/02/81375.php  
  
Basically, in some places a local vertex type is used as the key and the global vertex type is used as the value. Without my change, these two types must be the same for the code to work.

---
