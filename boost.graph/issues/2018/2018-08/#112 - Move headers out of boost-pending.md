# #112 - Move headers out of boost/pending [Open]

> Username: jeking3  
> Created at: 2018-08-22 13:11:17 UTC  
> Updated at: 2026-02-20 21:39:31 UTC  
> Url: https://github.com/boostorg/graph/issues/112  

Headers were likely never supposed to live in boost/pending for a long time. At this point boost/pending should be retired and the headers moved into their rightful and normal place. Here are the pending headers for this repository:  
  
```  
graph/include/boost/pending/bucket_sorter.hpp  
graph/include/boost/pending/container_traits.hpp  
graph/include/boost/pending/detail/property.hpp  
graph/include/boost/pending/fenced_priority_queue.hpp  
graph/include/boost/pending/fibonacci_heap.hpp  
graph/include/boost/pending/indirect_cmp.hpp  
graph/include/boost/pending/is_heap.hpp  
graph/include/boost/pending/mutable_heap.hpp  
graph/include/boost/pending/mutable_queue.hpp  
graph/include/boost/pending/property.hpp  
graph/include/boost/pending/queue.hpp  
graph/include/boost/pending/relaxed_heap.hpp  
graph/include/boost/pending/stringtok.hpp  
```

---

## Comment 1

> Username: andreacassioli  
> Created at: 2026-02-17 20:51:09 UTC  
> Url: https://github.com/boostorg/graph/issues/112#issuecomment-3917027766  

Looks like those files are still there. Are all of them still relevant? Maybe switch to use the STL? WHat do you think @jeremy-murphy  ?

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2026-02-17 21:05:32 UTC  
> Url: https://github.com/boostorg/graph/issues/112#issuecomment-3917086736  

As the name suggests, those files were presumably "pending" a review to be moved into another library, but that review and migration never happened.   
I'm pretty sure they are still used and are customised for the BGL, so there is no standard alternative to drop in as a replacement.  
It would be nice to tidy them up one day, but it's just not a high priority for me. Yes, they're a bit of a wart, but one that I can live with. 😅

---

## Comment 3

> Username: andreacassioli  
> Created at: 2026-02-19 20:03:06 UTC  
> Url: https://github.com/boostorg/graph/issues/112#issuecomment-3929632525  

OK, fair enough. Do you know what is the reason for the customisation?

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2026-02-20 21:39:31 UTC  
> Url: https://github.com/boostorg/graph/issues/112#issuecomment-3937282236  

I'm not sure, sorry.
