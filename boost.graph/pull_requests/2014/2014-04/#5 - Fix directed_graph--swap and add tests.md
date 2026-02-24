# #5 Fix [un]directed_graph::swap and add tests [Merged]

> Username: BenPope  
> Created at: 2014-04-21 14:16:42 UTC  
> Updated at: 2014-06-12 05:44:49 UTC  
> Merged at: 2014-05-14 16:41:25 UTC  
> Closed at: 2014-05-14 16:41:25 UTC  
> Url: https://github.com/boostorg/graph/pull/5  



---

## Comment 1

> Username: BenPope  
> Created_at: 2014-05-14 16:13:18 UTC  
> Url: https://github.com/boostorg/graph/pull/5#issuecomment-43101760  

Some other points you might like to comment on:  
- adjacency_list::Base (detail::adj_list_gen) appears to be contain no members, but is copied and assigned, whilst maybe_named_graph appears to contain members, but is not copied or assigned.  
- adjacency_list::swap looks suboptimal (copying, as it does).  
  
Should I look into reimplementing adjacency_list::swap to be cheap and correct, and then reimplement copy and assignment in the idiomatic way?  
  
I'm also considering free function swap, but that didn't seem to work when I gave it a quick try, perhaps because it should exist in the boost namespace (for ADL) and doesn't get picked up over boost/utility/swap, or I may have messed up the implementation.

---

## Comment 2

> Username: BenPope  
> Created_at: 2014-05-14 16:43:27 UTC  
> Updated_at: 2014-05-14 16:44:27 UTC  
> Url: https://github.com/boostorg/graph/pull/5#issuecomment-43105670  

That was quick, nice one!

---
