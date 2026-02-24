# #4 foreach: work around nameclash with the Qt foreach macro [Open]

> Username: timblechmann  
> Created at: 2015-03-03 12:27:03 UTC  
> Updated at: 2015-03-04 22:29:32 UTC  
> Url: https://github.com/boostorg/foreach/pull/4  



---

## Comment 1

> Username: ericniebler  
> Created_at: 2015-03-04 17:58:04 UTC  
> Url: https://github.com/boostorg/foreach/pull/4#issuecomment-77210015  

Does this patch even help? It makes it possible to #include foreach.hpp without errors, but does it make it possible to use `BOOST_FOREACH` in Qt translation units?

---

## Comment 2

> Username: timblechmann  
> Created_at: 2015-03-04 22:29:32 UTC  
> Url: https://github.com/boostorg/foreach/pull/4#issuecomment-77264370  

sure. one can use both BOOST_FORECH and foreach (as alias for Q_FOREACH). multi_index will need a similar patch to guard `boost::foreach::tag`, though.

---
