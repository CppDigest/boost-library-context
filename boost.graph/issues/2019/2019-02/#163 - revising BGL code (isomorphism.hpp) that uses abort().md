# #163 - revising BGL code (isomorphism.hpp) that uses abort() [Open]

> Username: vjcitn  
> Created at: 2019-02-12 17:11:20 UTC  
> Updated at: 2020-06-30 00:51:07 UTC  
> Url: https://github.com/boostorg/graph/issues/163  

https://github.com/boostorg/graph/blob/887e63d34e977ae439e392282422d2b82791a730/include/boost/graph/isomorphism.hpp#L321 is an abort() call.  This is a problem for interoperation with R as the static checker warns about such calls, and warnings preclude inclusion of packages in certain ecosystems.  
  
There don't seem to be many instances of abort() in BGL code.  Should it be replaced by a boost::throw_exception?

---

## Comment 1

> Username: anadon  
> Created at: 2019-04-05 20:38:35 UTC  
> Url: https://github.com/boostorg/graph/issues/163#issuecomment-480414562  

Quite possibly.  I'll ask about it.  Thank you for bringing this to attention.

---

## Comment 2

> Username: anadon  
> Created at: 2019-04-06 04:06:30 UTC  
> Url: https://github.com/boostorg/graph/issues/163#issuecomment-480472223  

Looks like the general advice is to avoid using aborts, with Peter Dimov pointing me towards https://github.com/boostorg/graph/blob/develop/include/boost/graph/isomorphism.hpp#L316 .  This seems like an easy PR, so would you like the credit?  If not, I should be able to get to it this weekend.

---

## Comment 3

> Username: vjcitn  
> Created at: 2019-04-06 09:18:20 UTC  
> Url: https://github.com/boostorg/graph/issues/163#issuecomment-480488901  

I am not concerned with getting credit.  There are a few other instances in graph:  
  
```  
%vjcair> # #define BOOST_LIB_VERSION "1_69"  
%vjcair> pwd  
/Library/Frameworks/R.framework/Resources/library/BH/include/boost/graph  
%vjcair> grep abort *  
grep: detail: Is a directory  
grep: distributed: Is a directory  
isomorphism.hpp:              abort();  
grep: parallel: Is a directory  
grep: planar_detail: Is a directory  
grep: property_maps: Is a directory  
%vjcair> grep abort */*  
distributed/adjacency_list.hpp:      abort();  
grep: distributed/adjlist: Is a directory  
grep: distributed/detail: Is a directory  
grep: parallel/detail: Is a directory  
%vjcair> grep abort */*/*  
distributed/detail/mpi_process_group.ipp:    abort();  
distributed/detail/mpi_process_group.ipp:    abort();  
```

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2020-06-30 00:51:06 UTC  
> Url: https://github.com/boostorg/graph/issues/163#issuecomment-651449313  

I just happened to notice this issue and yeah, wow, library code should _never_ call `abort()` or `exit()`, except in the guise of an assert, which the code in `graph/include/boost/graph/isomorphism.hpp` does _as well_. Weird.  
The `abort()` was introduced in 2012 as part of a fix to the algorithm and I guess it escaped review, as the same change also introduces considerable use of `goto`.  :\ Now I'm not saying that `goto` is universally bad, but these dubious changes together are... suspicious.  
  
I wouldn't recommend replacing it with throwing an exception; the `abort()` is really just an over-eager debugging tool.
