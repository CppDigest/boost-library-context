# #401 - copy_async's check that InputIterator is contiguous should be a static_assert [Closed]

> Username: jbytheway  
> Created at: 2014-12-31 04:11:58 UTC  
> Updated at: 2015-01-06 16:28:36 UTC  
> Closed at: 2015-01-06 16:28:36 UTC  
> Url: https://github.com/boostorg/compute/issues/401  

Currently copy_async and dispatch_copy_async check is_contiguous_iterator&lt;InputIterator&gt;::value and assert if it's not true.  This is a compile-time condition and would be much more helpful if checked with a static_assert rather than a runtime one.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-12-31 05:05:23 UTC  
> Url: https://github.com/boostorg/compute/issues/401#issuecomment-68423972  

Implemented in PR #404.
