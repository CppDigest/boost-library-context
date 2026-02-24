# #19 Fix polygon_traits.hpp and iterator_geometry_to_set.hpp for compatibility with C++17 [Merged]

> Username: plopresti  
> Created at: 2018-09-10 00:51:56 UTC  
> Updated at: 2018-09-16 09:59:02 UTC  
> Merged at: 2018-09-16 09:59:02 UTC  
> Closed at: 2018-09-16 09:59:02 UTC  
> Url: https://github.com/boostorg/polygon/pull/19  

C++17 adds std::size() which breaks all sorts of things, including polygon_traits.hpp and iterator_geometry_to_set.hpp. See https://quuxplusone.github.io/blog/2018/06/17/std-size/ for a description of this problem in general and https://lists.boost.org/Archives/boost/2015/04/221694.php for a report on this instance in particular.  
  
The fix is a little ugly but very straightforward: Use fully-qualified calls to size() wherever ambiguities are possible.

---

## Comment 1

> Username: asydorchuk  
> Created_at: 2018-09-16 09:58:58 UTC  
> Url: https://github.com/boostorg/polygon/pull/19#issuecomment-421735523  

@plopresti thanks for the fix!

---
