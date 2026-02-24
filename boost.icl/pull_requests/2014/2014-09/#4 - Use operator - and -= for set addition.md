# #4 Use operator | and |= for set addition [Closed]

> Username: changlan  
> Created at: 2014-09-17 23:31:31 UTC  
> Updated at: 2014-09-18 01:42:07 UTC  
> Closed at: 2014-09-18 01:40:33 UTC  
> Url: https://github.com/boostorg/icl/pull/4  

As mentioned in the document (http://www.boost.org/doc/libs/1_56_0/libs/icl/doc/html/boost_icl/function_reference/addition.html), a set union semantics is often attached operators |= and |. This is the case, for example, when using boost::dynamic_bitset, which does not provide operators += and + for set union semantics.   
  
However, the original implementation of operators |= and | still uses += and + for the underlying set container. This makes operators |= and | less useful. Furthermore, we can't use boost::dynamic_bitset for icl objects currently. I think this is an unnecessary limitation.  
  
A proper fix would be changing += and + into |= and | when users call the |= and | operators. The code will be more consistent with the statement above.

---

## Comment 1

> Username: changlan  
> Created_at: 2014-09-18 01:42:07 UTC  
> Url: https://github.com/boostorg/icl/pull/4#issuecomment-55985311  

This patch is not complete. Wait until I finishes patching.

---
