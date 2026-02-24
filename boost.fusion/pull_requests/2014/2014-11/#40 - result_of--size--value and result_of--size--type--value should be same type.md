# #40 result_of::size::value and result_of::size::type::value should be same type [Merged]

> Username: Flast  
> Created at: 2014-11-18 17:20:25 UTC  
> Updated at: 2014-11-19 01:46:46 UTC  
> Merged at: 2014-11-18 22:22:41 UTC  
> Closed at: 2014-11-18 22:22:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/40  

Split up from #39   
  
Fix metafunction type mismatch, which is described in [#7304](https://svn.boost.org/trac/boost/ticket/7304). However, still stay on wontfix status.  
e.g. In C++03 mode, `result_of::size<deque<...> >::value` has `int`, but `result_of::size<deque<...> >::type::value_type` has `long`.

---
