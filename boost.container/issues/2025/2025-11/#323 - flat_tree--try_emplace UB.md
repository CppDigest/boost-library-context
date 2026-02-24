# #323 - flat_tree::try_emplace UB [Closed]

> Username: psiha  
> Created at: 2025-11-17 14:18:00 UTC  
> Updated at: 2025-12-06 10:03:32 UTC  
> Closed at: 2025-12-06 10:03:32 UTC  
> Url: https://github.com/boostorg/container/issues/323  

The line (1050 from v1.88)  
`ret.second = hint == const_iterator()`  
triggers an assertion failure (w/ a custom container and MSVC secure STL) - as the hint interator is not "technically"/'by the standard' comparable to a default constructed one.  
In face/IMO you should not even have this check to begin with - if someone does not have a hint iterator they can use a different function or the begin or end iterator...

---

## Comment 1

> Username: PabloMK7  
> Created at: 2025-12-04 15:25:53 UTC  
> Updated at: 2025-12-04 15:27:17 UTC  
> Url: https://github.com/boostorg/container/issues/323#issuecomment-3612792817  

We are actually affected by this issue (or a very similar one), which causes some of the emplaced items into a `flat_map` not actually getting emplaced. The resulting flat map ends up with half the intended items.  
https://github.com/azahar-emu/azahar/blob/346bcf02ea7df30e4fba95d230860cdb87a8ac50/src/core/hle/service/service.cpp#L151  
  
Started happening with vc145. Happening in boost 1.82 and 1.89

---

## Comment 2

> Username: igaztanaga  
> Created at: 2025-12-06 09:30:16 UTC  
> Updated at: 2025-12-06 09:31:25 UTC  
> Url: https://github.com/boostorg/container/issues/323#issuecomment-3619806852  

This problem also affects boost::container::map and insert_or_assign overloads.
