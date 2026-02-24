# #168 Fix/increment operator for point and segment iterator [Merged]

> Username: mkaravel  
> Created at: 2014-10-24 08:19:08 UTC  
> Updated at: 2014-10-31 13:54:55 UTC  
> Merged at: 2014-10-31 13:21:27 UTC  
> Closed at: 2014-10-31 13:21:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/168  

This is a follow up of PR #167. Following private discussions, the modifications in this PR will not make it to boost 1.57. As a result PR #167 is closed.  
  
The current PR is against the `develop` branch rather than the `develop-1.57` branch.  
  
--- Copying from the PR #167 ---  
  
In this PR:  
- The increment/decrement operators for `point_iterator<>` and `segment_iterator<>` are properly defined within the corresponding class, rather than inherited from the base class. This fixes compilation problems for the following type of expressions:  
  
```  
for (iterator it = ++first; it != last; ++it) { /* do something here */ }  
```  
  
and  
  
```  
iterator it2 = ++it1;  
```  
  
where iterator above stands for either point or segment iterator.  
- Concept checks from the Boost.Iterator and Boost.ConceptCheck libraries are added in the point/segment iterator unit tests.  
- The iterator category for the `segment_iterator<>` is correctly defined to be `std::bidirectional_iterator_tag`.

---
