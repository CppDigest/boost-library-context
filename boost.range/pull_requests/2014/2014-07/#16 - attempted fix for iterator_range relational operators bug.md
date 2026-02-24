# #16 attempted fix for iterator_range relational operators bug [Merged]

> Username: ericniebler  
> Created at: 2014-07-30 00:57:32 UTC  
> Updated at: 2014-07-30 14:53:31 UTC  
> Merged at: 2014-07-30 14:53:31 UTC  
> Closed at: 2014-07-30 14:53:31 UTC  
> Url: https://github.com/boostorg/range/pull/16  

Looks like a lot, but it mostly a revert of the iterator_range relational operators, with an extra enable_if to avoid regressing https://svn.boost.org/trac/boost/ticket/6715. Also, adds a test.

---
