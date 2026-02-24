# #1 tuple: silence gcc warning [Merged]

> Username: timblechmann  
> Created at: 2013-12-07 11:23:59 UTC  
> Updated at: 2014-06-12 07:50:12 UTC  
> Merged at: 2014-04-17 00:53:51 UTC  
> Closed at: 2014-04-17 00:53:51 UTC  
> Url: https://github.com/boostorg/tuple/pull/1  

fix warning:  
  
```  
../../../boost/tuple/detail/tuple_basic.hpp: In function ‘typename boost::tuples::access_traits<typename boost::tuples::element<N, boost::tuples::cons<HT, TT> >::type>::const_type boost::tuples::get(const boost::tuples::cons<HT, TT>&)’:  
../../../boost/tuple/detail/tuple_basic.hpp:228:45: warning: typedef ‘cons_element’ locally defined but not used [-Wunused-local-typedefs]  
   typedef BOOST_DEDUCED_TYPENAME impl::type cons_element;  
```

---

## Comment 1

> Username: Lastique  
> Created_at: 2014-03-08 08:39:16 UTC  
> Url: https://github.com/boostorg/tuple/pull/1#issuecomment-37092638  

Note I created a similar pull request https://github.com/boostorg/tuple/pull/2 which removes the unused typedef to silence the warning.

---
