# #1169 - [regression] MSVC Warning C4459: declaration of 'indices' hides global declaration [Closed]

> Username: wanghan02  
> Created at: 2023-07-03 09:24:54 UTC  
> Updated at: 2023-09-14 17:20:59 UTC  
> Closed at: 2023-09-14 17:20:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1169  

There is a fixed [ticket#835](https://github.com/boostorg/geometry/issues/835). Now the warning pops out again with boost 1.82.0 at a different location. MSVC version is VS2022 17.1.0.  
  
```  
boost_1_82_0\boost\geometry\algorithms\detail\overlay\colocate_clusters.hpp(89,1): warning C4459: declaration of 'indices' hides global declaration  
boost_1_82_0\boost\multi_array\base.hpp(70,32): note: see declaration of 'boost::`anonymous-namespace'::indices'  
boost_1_82_0\boost\geometry\algorithms\detail\overlay\handle_colocations.hpp(100): note: see reference to function template instantiation 'void boost::geometry::detail::overlay::colocate_clusters<Clusters,Turns>(const Clusters &,Turns &)' being compiled  
        with  
        [  
            Clusters=cluster_type,  
            Turns=turn_container_type  
        ]  
```

---

## Comment 1

> Username: wanghan02  
> Created at: 2023-08-28 12:49:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/1169#issuecomment-1695642059  

This is still a problem in boost 1.83.0.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2023-09-14 17:20:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1169#issuecomment-1719853567  

Fixed by #1202
