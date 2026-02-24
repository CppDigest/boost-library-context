# #40 qualify boost::range_[const|mutable]_iterator [Merged]

> Username: treh  
> Created at: 2015-11-04 09:06:22 UTC  
> Updated at: 2015-11-05 11:42:43 UTC  
> Merged at: 2015-11-05 11:42:43 UTC  
> Closed at: 2015-11-05 11:42:43 UTC  
> Url: https://github.com/boostorg/range/pull/40  

Dear Neil,  
  
`boost::has_range_iterator`  
currently “calls” `boost::range_detail::has_range_iterator_impl`,   
which in turn “calls” `range_mutable_iterator`.  
Due to the namespace being in, this boils down to `boost::range_detail::range_mutable_iterator`  
and not `boost::range_mutable_iterator`.   
I expected the latter, because otherwise specializing this function as described in http://www.boost.org/doc/libs/1_59_0/libs/range/doc/html/range/reference/extending/method_2.html has no effect.  
  
Tobi

---

## Comment 1

> Username: treh  
> Created_at: 2015-11-05 11:30:23 UTC  
> Url: https://github.com/boostorg/range/pull/40#issuecomment-154036278  

Also see https://svn.boost.org/trac/boost/ticket/11785

---
