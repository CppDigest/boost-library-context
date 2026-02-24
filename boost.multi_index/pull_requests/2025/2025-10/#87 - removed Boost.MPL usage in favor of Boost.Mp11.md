# #87 removed Boost.MPL usage in favor of Boost.Mp11 [Merged]

> Username: joaquintides  
> Created at: 2025-10-25 19:21:10 UTC  
> Updated at: 2025-10-27 08:17:52 UTC  
> Merged at: 2025-10-27 08:17:49 UTC  
> Closed at: 2025-10-27 08:17:49 UTC  
> Url: https://github.com/boostorg/multi_index/pull/87  

Closes #34.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2025-10-25 19:38:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multi_index/pull/87#pullrequestreview-3380215102  

📁 include/boost/multi_index/detail/node_type.hpp

```diff
  54 |-     IndexSpecifierList,
  44 |+   typedef mp11::mp_reverse_fold<
  45 |+     mp11::mp_rename<detail::mp11_index_list<IndexSpecifierList>,mp11::mp_list>,
```

> Username: pdimov  
> Created_at: 2025-10-25 19:38:24 UTC  
> Url: https://github.com/boostorg/multi_index/pull/87#discussion_r2463009634  

This workaround shouldn't be necessary any longer - I fixed it.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2025-10-25 19:41:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multi_index/pull/87#pullrequestreview-3380215862  

📁 test/Jamfile.v2

```diff
  48 |+       <library>/boost/tuple//boost_tuple
  49 |+       <library>/boost/type_traits//boost_type_traits
  50 |+       <library>/boost/utility//boost_utility
```

> Username: pdimov  
> Created_at: 2025-10-25 19:41:26 UTC  
> Url: https://github.com/boostorg/multi_index/pull/87#discussion_r2463010625  

Do you really use all these? Directly?

> Username: joaquintides  
> Created_at: 2025-10-25 20:34:32 UTC  
> Updated_at: 2025-10-25 20:34:33 UTC  
> Url: https://github.com/boostorg/multi_index/pull/87#discussion_r2463027196  

I've pasted the output of `boostdep --test multi_index`, so I guess this is really it?

> Username: pdimov  
> Created_at: 2025-10-25 23:39:46 UTC  
> Url: https://github.com/boostorg/multi_index/pull/87#discussion_r2463127415  

You only need the direct dependencies here. --test gives you everything, including secondaries.  
  
For the primary dependencies used by your tests, use `boostdep --track-tests --primary multi_index`, but that's going to also give you the dependencies of the multi_index headers, which you don't need to replicate here.  
  
There's no boostdep command that will tell you what your tests need in addition to what you already get from `<library>multi_index`.  
  
(From looking at the diff of the two boostdep outputs, I think that the answer is `foreach` and `serialization`.)

> Username: joaquintides  
> Created_at: 2025-10-26 10:37:41 UTC  
> Url: https://github.com/boostorg/multi_index/pull/87#discussion_r2463703554  

Trimmed it down to  
```  
foreach  
iterator  
mpl  
serialization  
```  
I wonder if `boostdep` should have an option to explicit calculate this, as it is exactly what Boost authors will need for their CML.txts/Jamfiles.

> Username: pdimov  
> Created_at: 2025-10-26 10:48:31 UTC  
> Url: https://github.com/boostorg/multi_index/pull/87#discussion_r2463727949  

Maybe. In general I prefer to only link the tests using Serialization to Boost::serialization, rather than all of them, but sometimes it's easier this way.

> Username: joaquintides  
> Created_at: 2025-10-26 11:29:38 UTC  
> Url: https://github.com/boostorg/multi_index/pull/87#discussion_r2463745876  

In the case of serialization, I've done it your way too.


---
