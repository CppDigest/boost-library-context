# #117 Replace include of outdated boost/cast.hpp header [Merged]

> Username: Mike-Devel  
> Created at: 2020-11-19 13:27:23 UTC  
> Updated at: 2020-12-12 14:33:53 UTC  
> Merged at: 2020-12-12 14:21:50 UTC  
> Closed at: 2020-12-12 14:21:50 UTC  
> Url: https://github.com/boostorg/range/pull/117  

- Instead include `boost/polymorphic_cast.hpp` directly.  
- This replaces the dependency of Boost.Range on Boost.NumericConversion with a direct dependency on Boost.Conversion (on which it anyway depends transitively via Boost.Iterator

---

## Comment 1

> Username: Mike-Devel  
> Created_at: 2020-12-12 10:35:10 UTC  
> Url: https://github.com/boostorg/range/pull/117#issuecomment-743736600  

Ping.  
Btw.: the test failure on mac is already present on develop and master and thus I don't beleive this PR has anything to do with it.

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2020-12-12 14:15:11 UTC  
> Updated_at: 2020-12-12 14:15:23 UTC  
> Url: https://github.com/boostorg/range/pull/117#issuecomment-743762398  

Food for thought:  
  
It seems the only reason this is included at all is the use of polymorphic_downcast here:  
  
https://github.com/boostorg/range/blob/486c74fbeba9ee9975e6d68e974230ec8ec22fb0/include/boost/range/detail/any_iterator_wrapper.hpp#L28  
  
Looking at the implementation of polymorphic_downcast:  
  
https://github.com/boostorg/conversion/blob/9540be42b4fa1ea66ae850f233e3ddff45e9efeb/include/boost/polymorphic_cast.hpp#L97-L102  
  
you could probably just as well inline the two lines of code and get rid of the dependency completely, but I didn't know this is something desireable for you. The PR as it is just cuts out a deprecated header out of the dependency chain, without changing the code.

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2020-12-12 14:33:50 UTC  
> Url: https://github.com/boostorg/range/pull/117#issuecomment-743764729  

Thanks

---
