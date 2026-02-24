# #1054 - help for non-convecity polygon generate by points [Closed]

> Username: yulinhuyang  
> Created at: 2022-08-18 13:44:01 UTC  
> Updated at: 2022-08-18 14:05:19 UTC  
> Closed at: 2022-08-18 14:05:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/1054  

I used some lidar scanned boundary points to generate polygons. But these points can't fully guarantee the order. resulting in non-convexity polygon. I use bg::correct to try to fix it. It can solve some of the problems, but some still don't work. Is there any other way to generate a polygon with a compact outer frame?  
  
![image](https://user-images.githubusercontent.com/13074332/185410049-1cae9d1a-b129-475f-8c52-ef5236343f86.png)  
  
  
like this,two lines crossed

---

## Comment 1

> Username: yulinhuyang  
> Created at: 2022-08-18 13:45:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/1054#issuecomment-1219513206  

I want get compact frame, so bg::convex_hull is not a good choice, It may generate a big frame
