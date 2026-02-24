# #67 - [voronoi] issue with pss() [Open]

> Username: eadf  
> Created at: 2021-07-11 18:26:49 UTC  
> Updated at: 2021-12-04 22:48:02 UTC  
> Url: https://github.com/boostorg/polygon/issues/67  

1.76.0 produces this voronoi output:  
  
## 1  
  
```  
0  
3  
673 903 -985 -362  
-238 248 -179 453  
-136 323 -90 419  
```  
![1](https://user-images.githubusercontent.com/655495/125205628-7ebf6e00-e283-11eb-9182-d6b409188798.png)  
  
here`pss([-985,-362],[-179,453,-238,248],[673,903,-985,-362])` finds a CE @[-2674.2,1851.9];  
  
![1](https://user-images.githubusercontent.com/655495/125636267-50d6a801-fba4-4f0c-922b-903e305d0c0d.png)  
  
  
## 2  
  
```  
0  
3  
519 -635 342 -158  
-440 707 661 -976  
269 -113 507 -171  
```  
![2](https://user-images.githubusercontent.com/655495/125205644-8da62080-e283-11eb-8c50-49e57c856069.png)  
  
here `pss([519,-635],[519,-635,342,-158],[269,-113,507,-171])` finds a CE @ [788.2,-535.1]  
  
![2](https://user-images.githubusercontent.com/655495/125635578-7e8eb01a-d4ab-4b2b-8e56-7004720a7186.png)  
  
## 3  
  
```  
0  
3  
597 -23 533 65  
716 302 455 245  
-716 412 999 356  
```  
![3](https://user-images.githubusercontent.com/655495/125206196-32296200-e286-11eb-90b0-ef481c65d2fb.png)  
  
here `pss([-716,412],[-716,412,999,356],[533,65,597,-23])` finds a CE @ [-784.19,-1676.38]  
  
![3](https://user-images.githubusercontent.com/655495/125634640-5730b348-f027-4bc2-98f2-87438b966e0c.png)  
  
## 4  
  
in example ”primary_29.txt" `pss([-1,1], [0,1,0,0], [0,0,-1,1])` finds a CE @ [-0.59,1.41]  
  
![4](https://user-images.githubusercontent.com/655495/125648472-60109e3e-999f-4b8a-a409-2eb4acf9528e.png)  
  
  
  
In these examples `pss()` solves for CE solutions where the (infinite line) segments tangents the CE, but the segment falls short of reaching that tangent point.

---

## Comment 1

> Username: ghost  
> Created at: 2021-11-22 19:19:47 UTC  
> Url: https://github.com/boostorg/polygon/issues/67#issuecomment-975843318  

@eadf have you had a chance to work on a fix for `pss()`? In any case, I'd be interested to read your thoughts on it (edges cases you've identified etc.)

---

## Comment 2

> Username: eadf  
> Created at: 2021-11-24 22:50:28 UTC  
> Updated at: 2021-11-26 23:59:08 UTC  
> Url: https://github.com/boostorg/polygon/issues/67#issuecomment-978392609  

@vm-am I made an effort to try to figure out the edge cases, but they just kept popping up.   
For example: if trying to delegate a `pss()` call to `pps()` it gladly finds circle events that are too close to the reduced-to-point-segment.  
  
But I will take a look at it again next week or so

---

## Comment 3

> Username: ghost  
> Created at: 2021-11-29 18:56:29 UTC  
> Url: https://github.com/boostorg/polygon/issues/67#issuecomment-981921042  

@eadf in your example, are both segments missing the circle? Would you please share the input to `pss()`?   
I have also been trying to reduce `pss()` to `pps()` (as well as `sss()` to `pss()` in some cases) by replacing by a point the segment that is the closest to the circle.    
  
It seems to work okay (so far) but I believe we'd better fix this issue during the construction of the circle rather than after the fact and risk to hit more edge cases.  
@asydorchuk any thoughts?

---

## Comment 4

> Username: eadf  
> Created at: 2021-11-30 11:41:10 UTC  
> Updated at: 2021-12-04 22:48:02 UTC  
> Url: https://github.com/boostorg/polygon/issues/67#issuecomment-982556738  

@vm-am  
  
> in your example, are both segments missing the circle?   
  
In my `pss()` test code I sort the two dot products and run `pps()` on the segment point with the largest error. But I think I've seen examples of both segments not reaching the CE.  
  
> Would you please share the input to `pss()`?  
  
I've got a `pss()` example where both segments misses the CE here:  
```  
0  
3  
-5138 -5149 -5038 -5142  
-5042 -5069 -5165 -5162  
-5011 -5195 -5404 -5134  
```  
The code "finds" a circle event at (-4953.51, -5113.77)   
with `site1`=(-5038,-5142) `site2`=(-5011,-5195)(-5404,-5134) `site3`=(-5165,-5162)(-5042,-5069)  
![double_pss](https://user-images.githubusercontent.com/655495/144717725-548db183-db15-4209-a1bd-fa1a1473b59d.png)  
  
  
> I believe we'd better fix this issue during the construction of the circle rather than after the fact and risk to hit more edge cases.  
  
☝️ I think replacing `pps()` with `ppp()` (or any combination thereof) was a bad idea. The information needed to ensure that the CE circle does not intersect the segment is *required* (the CE circle should only touch the endpoints or tangent the segment)  
I've changed my `pps()` -> `ppp()` PR accordingly.  
  
Implementing a CE function that takes the length of the segment into consideration the correct way using `sqrt_expr_evaluator_XXX()` (or something similar) is probably beyond my math skills.
