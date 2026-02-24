# #985 - disjoint does not work for const points [Closed]

> Username: barendgehrels  
> Created at: 2022-03-09 09:49:48 UTC  
> Updated at: 2022-04-13 08:09:53 UTC  
> Closed at: 2022-04-13 08:09:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/985  

A disjoint of two geometries of which both points are const (or one of them) does not work, because internally those point types are used to calculate turns
