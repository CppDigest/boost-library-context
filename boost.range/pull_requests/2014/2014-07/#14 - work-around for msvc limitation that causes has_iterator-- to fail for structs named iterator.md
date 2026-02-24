# #14 work-around for msvc limitation that causes has_iterator<> to fail for structs named iterator [Merged]

> Username: ericniebler  
> Created at: 2014-07-14 07:08:46 UTC  
> Updated at: 2020-03-01 20:22:59 UTC  
> Merged at: 2014-07-15 09:06:31 UTC  
> Closed at: 2014-07-15 09:06:31 UTC  
> Url: https://github.com/boostorg/range/pull/14  

Only applies to msvc-10.0 and earlier. This fixes the failures in boost.accumulators on those versions of msvc. I simply specializes `boost::range_detail::has_iterator` for all the classes and structs at namespace scope in boost named `iterator`. A dumb fix for a dumb compiler bug, but hey, it works.  
  
Ran full range regressions for all versions of msvc from 8 through 12. No new failures.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-03-01 20:22:58 UTC  
> Url: https://github.com/boostorg/range/pull/14#issuecomment-593140747  

@ericniebler is this workaround is still necessary? I was cleaning up dead code in Range and reverting this workaround does not break Accumulators on msvc-9/10 now. Probably the cause was #11 and https://github.com/boostorg/mpl/pull/8 fixed the issue?

---
