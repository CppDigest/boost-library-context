# #167 Fix/increment operator for point and segment iterator [Closed]

> Username: mkaravel  
> Created at: 2014-10-23 22:06:34 UTC  
> Updated at: 2014-10-31 13:54:55 UTC  
> Closed at: 2014-10-24 08:13:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/167  

In this PR:  
- The increment/decrement operators for `point_iterator<>` and `segment_iterator<>` are properly defined within the corresponding class, rather than inherited from the base class. This fixes compilation problems for the following type of expressions:  
  
```  
for (iterator it = ++first; it != last; ++it) { /* do something here */ }  
```  
  
and  
  
```  
iterator it2 = ++it1;  
```  
  
where `iterator` above stands for either point or segment iterator  
- Concept checks from the Boost.Iterator and Boost.ConceptCheck libraries are added in the point/segment iterator unit tests.  
- The iterator category for the `segment_iterator<>` is correctly defined to be `std::bidirectional_iterator_tag`.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-10-24 08:06:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/167#issuecomment-60356586  

Please don't merge yet.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-10-24 08:13:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/167#issuecomment-60357160  

@barendgehrels I will close this PR and create another one against the develop branch.  
Let's leave it for 1.58.

---
