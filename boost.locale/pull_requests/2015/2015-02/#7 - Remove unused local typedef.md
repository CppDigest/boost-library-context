# #7 Remove unused local typedef. [Closed]

> Username: alkino  
> Created at: 2015-02-12 17:18:31 UTC  
> Updated at: 2017-07-13 14:03:11 UTC  
> Closed at: 2017-07-13 14:03:10 UTC  
> Url: https://github.com/boostorg/locale/pull/7  

It gives warning with gcc < 4.9  
  
```  
boost/include/boost/locale/boundary/segment.hpp: In function ‘int boost::locale::boundary::details::compare_text(LeftIterator, LeftIterator, RightIterator, RightIterator)’:  
boost/include/boost/locale/boundary/segment.hpp:30:35: attention : typedef ‘right_iterator’ locally defined but not used [-Wunused-local-typedefs]  
             typedef RightIterator right_iterator;  
```

---

## Comment 1

> Username: artyom-beilis  
> Created_at: 2017-07-13 14:03:10 UTC  
> Url: https://github.com/boostorg/locale/pull/7#issuecomment-315087251  

Already fixed

---
