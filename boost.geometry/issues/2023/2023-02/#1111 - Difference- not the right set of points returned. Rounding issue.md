# #1111 - Difference: not the right set of points returned. Rounding issue? [Closed]

> Username: ghost  
> Created at: 2023-02-01 06:59:20 UTC  
> Updated at: 2024-11-24 10:19:22 UTC  
> Closed at: 2024-11-24 10:19:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/1111  

Hi,  
  
Please have a look at [the issue on godbolt](https://godbolt.org/z/v94vceE1K).  
  
As you can see, points are disappearing when they should not.  
My issue is that I suspect rounding to remove some points that should be kept on the polygon after applying a difference.  
Missing points are my main concern, I've been caught because I use your library for game development.   
  
Type is double so we should not have such an issue with such large (0.05) absolute coordinates difference.  
  
I disabled the optimisation because otherwise Goldbolt complained about compilation time too long (may be a temporary issue).  
  
Is there an immediate workaround ?  
  
Thanks

---

## Comment 1

> Username: ghost  
> Created at: 2023-02-01 07:54:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/1111#issuecomment-1411605863  

The issue arise because the points are touching the segment.  
  
Maybe at some point the algorithm decides that they are unimportant, but they are to me. They work as tags/locations markers.  
  
Applying a very small buffer could be an immediate workaround but it adds a lot of point and mess with the rest of my code.  
  
The only way to do it right now is to manually add the points back in a loop.  
  
Keeping the points in place on the edges in the first place would avoid this. Maybe add an option?

---

## Comment 2

> Username: barendgehrels  
> Created at: 2023-03-15 10:18:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/1111#issuecomment-1469732918  

The issue is gone on godbolt... `Whoops, ID "v94vceE1K" could not be found` Can you please add it again?

---

## Comment 3

> Username: vissarion  
> Created at: 2023-06-19 14:47:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/1111#issuecomment-1597320021  

I can reproduce it locally. To my understanding and according to difference definition (see [documentation](https://www.boost.org/doc/libs/1_82_0/libs/geometry/doc/html/geometry/reference/algorithms/difference/difference_3.html) the right output of the above program is should be   
```  
8, 10  
0, 10  
0, 0  
8, 0  
```  
for both `f1` and `f2`.    
  
However, boost geometry (1.82) returns   
```  
8 9.95  
8 10  
0 10  
0 0  
8 0  
```  
for `f1` and   
```  
8 8.95  
8 10  
0 10  
0 0  
8 0  
```   
for `f2`.   
  
@LumiereDeLOmbre it is not clear to me what you want to achieve but maybe talking the first polygon union the intersection of both polygons (only points could be an option) is a way to keep the intersection points you need.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2024-11-24 10:19:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/1111#issuecomment-2495921497  

Verified with 1.87 (beta), behaviour is fully OK AFAICS  
Closing issue.  
  
```  
"POLYGON((0 0,8 0,8 10,0 10,0 0))",  
"POLYGON((8 9,12 9,12 9.95,8 9.95,8 9))",  
"MULTIPOLYGON(((8 10,0 10,0 0,8 0,8 10)))",  
"POLYGON((8 8.5,12 8.5,12 8.95,8 8.95,8 8.5))",      
```  
->  
```  
issue_1111_a<d> a: 80 b: 3.8 union: 83.8 intersection: 0 diff(a-b): 80 diff(b-a): 3.8 sym_diff: 83.8    checks: 0 0 0 0 OK OK OK OK  
issue_1111_b<d> a: 80 b: 1.8 union: 81.8 intersection: 0 diff(a-b): 80 diff(b-a): 1.8 sym_diff: 81.8    checks: 0 0 0 0 OK OK OK OK  
  
```
