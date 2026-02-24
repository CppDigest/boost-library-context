# #90 - filter_iterator regression: m_storage is private within this context [Closed]

> Username: mglisse  
> Created at: 2025-04-01 18:38:31 UTC  
> Updated at: 2025-04-01 21:28:17 UTC  
> Closed at: 2025-04-01 21:27:37 UTC  
> Url: https://github.com/boostorg/iterator/issues/90  

https://github.com/boostorg/iterator/blob/eee670608e952e46e6be345900c9806a7233beb2/include/boost/iterator/filter_iterator.hpp#L102-L104  
  
AFAICS, since the modernization 2 months ago, the code tries to access the private `m_storage` of a different type (same template with different parameters) without being a friend. This causes errors for code that used to work fine.  
  
```c++  
#include <boost/iterator/filter_iterator.hpp>  
  
struct True { bool operator()(int){return true;} };  
typedef boost::filter_iterator<True, int*> X;  
typedef boost::filter_iterator<True, int const*> Y;  
Y f(X x){return x;}  
```  
```  
In file included from a.cc:1:  
.../boost/libs/iterator/include/boost/iterator/filter_iterator.hpp: In instantiation of ‘boost::iterators::filter_iterator<Predicate, Iterator>::filter_iterator(const boost::iterators::filter_iterator<Predicate, OtherIterator>&) [with OtherIterator = int*; <template-parameter-2-2> = boost::iterators::detail::enable_type; Predicate = True; Iterator = const int*]’:  
a.cc:6:17:   required from here  
    6 | Y f(X x){return x;}  
      |                 ^  
.../boost/libs/iterator/include/boost/iterator/filter_iterator.hpp:104:40: error: ‘boost::iterators::filter_iterator<True, int*>::storage boost::iterators::filter_iterator<True, int*>::m_storage’ is private within this context  
  104 |         super_t(t.base()), m_storage(t.m_storage.predicate(), m_storage.m_end)  
      |                                      ~~^~~~~~~~~  
.../boost/libs/iterator/include/boost/iterator/filter_iterator.hpp:129:13: note: declared private here  
  129 |     storage m_storage;  
      |             ^~~~~~~~~  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2025-04-01 21:28:16 UTC  
> Url: https://github.com/boostorg/iterator/issues/90#issuecomment-2770728031  

Thanks for the report, should be fixed now.
