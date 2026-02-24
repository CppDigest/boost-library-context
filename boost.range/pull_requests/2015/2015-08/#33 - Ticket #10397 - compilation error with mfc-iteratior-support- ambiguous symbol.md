# #33 Ticket #10397 - compilation error with mfc-iteratior-support: ambiguous symbol [Merged]

> Username: tobias-loew  
> Created at: 2015-08-12 13:35:41 UTC  
> Updated at: 2016-01-04 16:37:18 UTC  
> Merged at: 2016-01-04 16:37:18 UTC  
> Closed at: 2016-01-04 16:37:18 UTC  
> Url: https://github.com/boostorg/range/pull/33  

fixed compilation error for mfc-iteration-support  
  
changed  
boost::range_detail::range_const_iterator  
to  
boost::range_detail::range_const_iterator_helper  
  
to address compilation error when using mfc-iteration-support

---
