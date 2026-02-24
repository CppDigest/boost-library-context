# #40 Fix #11374 and #8791: issues with bundled properties and `find_flow_cost` [Closed]

> Username: maelvls  
> Created at: 2015-06-04 14:00:32 UTC  
> Updated at: 2016-03-02 17:22:10 UTC  
> Closed at: 2016-03-02 14:25:11 UTC  
> Url: https://github.com/boostorg/graph/pull/40  

I talked about that problem on [stackoverflow](http://stackoverflow.com/questions/30621255/using-named-parameters-and-bundled-properties-with-edmonds-karp-max-flow). This PR solves two different tickets because the cause was almost the same (bundled properties had not been tested thoroughly).    
Here are the tickets:   
- Issue [#11374](https://svn.boost.org/trac/boost/ticket/11374): `find_flow_cost()` won't work with bundled properties in conjonction with named parameters  
- Issue [#12038](https://svn.boost.org/trac/boost/ticket/12038): every max-flow function (e.g. `edmonds_arp_max_flow()`) won't work with named parameters   
  
I apologize for having mixed those two issues in one PR ; I did try to separate them, but the last one (bundled properties and named parameters with `find_flow_cost()`) needed the two other commits.

---

## Comment 1

> Username: maelvls  
> Created_at: 2016-03-02 13:57:21 UTC  
> Updated_at: 2016-03-02 14:25:37 UTC  
> Url: https://github.com/boostorg/graph/pull/40#issuecomment-191247442  

I'll create three different tickets because this PR won't be accepted with so many different fixes in one PR.

---
