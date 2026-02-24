# #1216 - Why doesn't dynamic_rstar have default constructor? [Closed]

> Username: sukeya  
> Created at: 2023-11-22 03:46:07 UTC  
> Updated at: 2023-12-12 12:52:10 UTC  
> Closed at: 2023-12-12 12:52:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/1216  

Hello,  
  
I'm facing a compile error at the following line because `dynamic_rstar` doesn't have default constructor.  
https://github.com/boostorg/geometry/blob/3f5c044abcc813a36d6af83465a9c086f9728a2f/include/boost/geometry/index/rtree.hpp#L364  
  
Is there any reason why it doesn't have default constructor?

---

## Comment 1

> Username: vissarion  
> Created at: 2023-12-04 13:02:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/1216#issuecomment-1838591642  

Could you please give more details about your compilation error?  
  
AFAIU you can create an rtree with rstar parameter algorithm either by a compile time parameter e.g.  
  
```bgi::rtree< value, bgi::rstar<16> > rtree;```  
  
or with a runtime one   
  
```bgi::rtree< value, bgi::dynamic_rstar> rtree2(bgi::dynamic_rstar(16)); ```

---

## Comment 2

> Username: sukeya  
> Created at: 2023-12-12 12:22:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/1216#issuecomment-1851932939  

I wrote a class like the following.  
  
```  
class A {  
  A() = default;  
  
  bgi::rtree<int, bgi::dynamic_rstar> rtree_;  
};  
```  
  
This code caused a compile error because a class `A` tried to construct `bgi::dynamic_tstar` using its default constructor. But, I fixed this compile error by setting default value to `rtree_` recently.  
I'm sorry for confusing you.
