# #8 Fix warning in variant.hpp [Closed]

> Username: apolukhin  
> Created at: 2015-02-09 17:19:49 UTC  
> Updated at: 2017-01-18 20:06:09 UTC  
> Closed at: 2017-01-18 20:06:09 UTC  
> Url: https://github.com/boostorg/serialization/pull/8  

Fix for the following warning:  
  
D:/Boost/boost/serialization/variant.hpp:69:75: warning: typedef 'types' locally defined but not used [-Wunused-local-typedefs]  
     typedef typename boost::variant<BOOST_VARIANT_ENUM_PARAMS(T)>::types types;

---

## Comment 1

> Username: apolukhin  
> Created_at: 2017-01-18 20:06:09 UTC  
> Url: https://github.com/boostorg/serialization/pull/8#issuecomment-273586037  

Looks like the issues was fixed

---
