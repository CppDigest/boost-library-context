# #538 Add workaround for msvc-12 faulty array initialization. [Merged]

> Username: awulkiew  
> Created at: 2018-12-10 22:06:19 UTC  
> Updated at: 2018-12-11 13:18:25 UTC  
> Merged at: 2018-12-11 13:18:25 UTC  
> Closed at: 2018-12-11 13:18:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/538  

This is a workaround for the following error:  
  
    boost/geometry/srs/projections/par_data.hpp(92) : error C2536: 'boost::geometry::srs::detail::towgs84<T>::boost::geometry::srs::detail::towgs84<T>::m_data' : cannot specify explicit initializer for arrays  
  
I added `core/config.hpp` file allowing us to add defines conveniently.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2018-12-10 23:05:34 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/538#pullrequestreview-183442731  

Thanks

📁 include/boost/geometry/core/config.hpp

```diff
  10 |+ #ifndef BOOST_GEOMETRY_CORE_CONFIG_HPP
  11 |+ #define BOOST_GEOMETRY_CORE_CONFIG_HPP
  12 |+ 
```

> Username: barendgehrels  
> Created_at: 2018-12-10 23:04:36 UTC  
> Updated_at: 2018-12-10 23:05:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/538#discussion_r240415907  

Cool, I've added this file too - for removing rescaling.  
Your's will be merged earlier so I will adapt/extend it


---
