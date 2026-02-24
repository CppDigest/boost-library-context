# #25 fix ticket #10754 [Merged]

> Username: levinmk  
> Created at: 2014-12-12 22:47:39 UTC  
> Updated at: 2015-01-28 09:05:22 UTC  
> Merged at: 2015-01-25 19:15:05 UTC  
> Closed at: 2015-01-25 19:15:05 UTC  
> Url: https://github.com/boostorg/range/pull/25  

as described in https://svn.boost.org/trac/boost/ticket/10754, postfix_increment_proxy and writable_postfix_increment_proxy were moved to boost::iterators::detail namespace in boost/iterator/iterator_facade.hpp

---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2015-01-27 22:11:26 UTC  
> Url: https://github.com/boostorg/range/pull/25#issuecomment-71738891  

This will probably result in a merge conflict - the other half of 1617a513d29b579d1cbda2af29b395ed31ee07e5 is still missing in master.

---

## Comment 2

> Username: pdimov  
> Created_at: 2015-01-27 23:09:38 UTC  
> Url: https://github.com/boostorg/range/pull/25#issuecomment-71748355  

There are many unmerged changes in develop, and yes, this does indeed cause a(n additional) merge conflict (there are a few others).

---

## Comment 3

> Username: neilgroves  
> Created_at: 2015-01-28 09:05:22 UTC  
> Url: https://github.com/boostorg/range/pull/25#issuecomment-71801446  

I can merge these later tonight.  
  
Neil  
On 27 Jan 2015 23:09, "Peter Dimov" notifications@github.com wrote:  
  
> There are many unmerged changes in develop, and yes, this does indeed  
> cause a(n additional) merge conflict (there are a few others).  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/range/pull/25#issuecomment-71748355.

---
