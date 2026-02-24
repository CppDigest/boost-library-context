# #4 flatten/flatten_view/ref [Merged]

> Username: jamboree  
> Created at: 2014-04-04 06:48:24 UTC  
> Updated at: 2014-05-01 04:48:45 UTC  
> Merged at: 2014-04-04 06:50:25 UTC  
> Closed at: 2014-04-04 06:50:25 UTC  
> Url: https://github.com/boostorg/fusion/pull/4  

New algorithm 'flatten' & view type 'flatten_view', plus support for boost::ref for working with mutable sequence.  
  
Adapted from https://svn.boost.org/trac/boost/ticket/9294

---

## Comment 1

> Username: djowel  
> Created_at: 2014-05-01 04:48:45 UTC  
> Url: https://github.com/boostorg/fusion/pull/4#issuecomment-41879941  

the support for boost::ref slipped through without proper review. what is the justification for it? how about a use case? is flatten dependent on it? I am reverting the boost::ref support, for now. Please add another pull request for it and we can discuss the merits there.  
  
this is the type of pull request that we need to void (where there are more than one feature being added at once).

---
