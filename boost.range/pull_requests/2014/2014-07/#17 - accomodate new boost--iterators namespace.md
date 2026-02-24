# #17 accomodate new boost::iterators namespace. [Merged]

> Username: ericniebler  
> Created at: 2014-07-30 01:19:43 UTC  
> Updated at: 2014-07-30 01:21:34 UTC  
> Merged at: 2014-07-30 01:21:34 UTC  
> Closed at: 2014-07-30 01:21:34 UTC  
> Url: https://github.com/boostorg/range/pull/17  

See boostorg/iterator@dc96d371faf87a88c04057861136c9dfa68103d7

---

## Comment 1

> Username: ericniebler  
> Created_at: 2014-07-30 01:21:29 UTC  
> Url: https://github.com/boostorg/range/pull/17#issuecomment-50562672  

This one seems uncontroversial, and the spurious failures will only obscure the situation with the iterator_range relational operators, so I'm just going ahead and merging this one. Note that this problem only occurs on develop since the change to Boost.Iterators hasn't been merged to master yet.

---
