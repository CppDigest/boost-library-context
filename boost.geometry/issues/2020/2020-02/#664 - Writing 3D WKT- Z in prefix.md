# #664 - Writing 3D WKT: Z in prefix? [Open]

> Username: sgiraudot  
> Created at: 2020-02-05 10:36:36 UTC  
> Updated at: 2021-01-11 15:29:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/664  

When writing 3D objects in WKT, a "Z" is supposed to be added to the prefix, such that a 2D point and a 3D point are differentiated:  
```  
POINT (1,2)  
POINT Z (1,2,3)  
```  
But as far as I tested, when writing 3D objects using the WKT writer of Boost Geometry, the "Z" prefix is never written, leading to this kind of output:  
  
```  
POINT (1,2)  
POINT (1,2,3)  
```  
It seems to be similar for other types of primitives (`LINESTRING`, etc.).  
  
Reading 3D objects with Boost both works with and without "Z" in the input.  
  
Wouldn't it make more sense to add "Z" to the prefix whenever a 3D object is written?

---

## Comment 1

> Username: mloskot  
> Created at: 2020-02-05 11:02:46 UTC  
> Updated at: 2020-02-05 11:05:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/664#issuecomment-582355046  

> When writing 3D objects in WKT, a "Z" is supposed to be added to the prefix,  
  
Yes, only when writing WKT as defined the ISO SQL/MM specification.  
  
Boost.Geometry is not compliant with the SQL/MM. The WKT I/O is based on the SFSQL 1.0 specification (still, I'm not betting my head it's fully compliant with it either).  
  
I'd suggest to consider the WKT I/O is a utility for user's convenience and for convenience of developers testing the library features itself. It is not really a fully-featured up-to-date production-ready and SQL/MM WKT implementation.  
  
-----  
  
It wouldn't take huge effort to add support for writing `Z` for 3D geometries. Question is, should it be optional, if not, what about compatibility with current implementation, should be retained or is it not relevant. Generally, Boost libraries never promise compatibility between versions. Plus, since WKT I/O is an utility feature, compatibility may not be a relevant issue. Let's see what others think.

---

## Comment 2

> Username: awulkiew  
> Created at: 2020-02-08 13:33:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/664#issuecomment-583736980  

So we'd need a separate function for that.  
  
We should also probably do it for `M`. The problem is currently we are unable between distinguishing between coordinates, measures, time, etc. It's all coordinates for us.

---

## Comment 3

> Username: mloskot  
> Created at: 2020-02-08 20:29:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/664#issuecomment-583773745  

I thought perhaps `detail::wkt::prefix_point` could be a template taking `Point` then it could dispatch based on dimension, `POINT` for 2 or `POINT Z` for 3.  
  
I think `Z` is more important than other measures. Another issue is that if BG advertises support of other measures, then users will likely expect e.g. why `M` is not copied/preserved/synthesised etc. by operation X :-)

---

## Comment 4

> Username: sudo-panda  
> Created at: 2020-03-12 10:09:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/664#issuecomment-598106107  

I would like to work on this. Is what @mloskot suggested final or does this need more inputs?

---

## Comment 5

> Username: mloskot  
> Created at: 2020-03-12 10:18:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/664#issuecomment-598109829  

@sudo-panda If my comment does not receive any responses and if my suggestion _seems sensible to you_, and you are willing to give it a go, then I'd suggest you to submit PR.  
  
Once there is a (working) proof of concept ready to review, it will be much easier for more people to offer specific feedback. It may turn out the idea is a good one and your PR will be accepted or we will find problems and your PR will need rework. You need to decide if you want to take the risk :)

---

## Comment 6

> Username: sudo-panda  
> Created at: 2020-03-13 05:03:50 UTC  
> Updated at: 2020-03-13 05:04:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/664#issuecomment-598550529  

@mloskot Since this appears to be closely related  to the header file I am already working on, do you think it will be better if I let that one get merged first?   
  
> I thought perhaps detail::wkt::prefix_point could be a template taking Point then it could dispatch based on dimension, POINT for 2 or POINT Z for 3.  
  
This seems like a detect approach but I think we should let the users decide which one to follow `POINT` or `POINT Z`. What do you say?

---

## Comment 7

> Username: mloskot  
> Created at: 2020-03-13 20:45:15 UTC  
> Updated at: 2020-03-13 20:45:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/664#issuecomment-598904008  

> Since this appears to be closely related to the header file I am already working on, do you think it will be better if I let that one get merged first?  
  
You need to link issue or PR or whatever you are talking about.   
  
> This seems like a detect approach  
  
I'm not familiar with this name, could you elaborate?  
  
>  I think we should let the users decide which one to follow `POINT` or `POINT Z`  
  
If user serialises `point_xy`, then obvious WKT output tag is `POINT`.  
If user serialises `point_xyz`, then obvious WKT output tag is `POINT Z`.  
  
Do you mean to offer user an option to force `POINT` output for `point_xyz` or `POINT Z` output for `point_xy`?

---

## Comment 8

> Username: sudo-panda  
> Created at: 2020-03-14 02:02:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/664#issuecomment-598996814  

> You need to link issue or PR or whatever you are talking about.  
  
I was talking about #670   
  
> > This seems like a detect approach  
>   
> I'm not familiar with this name, could you elaborate?  
  
Sorry, that was a typo. I wanted to say something on the line of this being a good approach.  
  
> > I think we should let the users decide which one to follow `POINT` or `POINT Z`  
>   
> If user serialises `point_xy`, then obvious WKT output tag is `POINT`.  
> If user serialises `point_xyz`, then obvious WKT output tag is `POINT Z`.  
>   
> Do you mean to offer user an option to force `POINT` output for `point_xyz` or `POINT Z` output for `point_xy`?  
  
I mean to let users decide whether to output `POINT` or `POINT_Z` for `point_xyz`. But I guess since boost doesn't have to maintain backwards compatibility it's fine if we don't give users that option.  
  
The only doubt that remains is should I start working on this before #670 is merged? In my opinion it is going to cause a lot of merge conflicts.

---

## Comment 9

> Username: Siddharth-coder13  
> Created at: 2020-12-01 10:53:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/664#issuecomment-736407789  

hey @mloskot, I am a new contributor and would like to work on this issue?

---

## Comment 10

> Username: mloskot  
> Created at: 2020-12-01 11:21:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/664#issuecomment-736451516  

@Siddharth-coder13 See https://github.com/boostorg/geometry/issues/368#issuecomment-719465037

---

## Comment 11

> Username: mloskot  
> Created at: 2020-12-11 15:56:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/664#issuecomment-743275630  

FYI, related comments posted in https://github.com/boostorg/geometry/pull/782#issuecomment-743275433

---

## Comment 12

> Username: vissarion  
> Created at: 2021-01-11 15:28:57 UTC  
> Updated at: 2021-01-11 15:29:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/664#issuecomment-758024105  

> I thought perhaps `detail::wkt::prefix_point` could be a template taking `Point` then it could dispatch based on dimension, `POINT` for 2 or `POINT Z` for 3.  
>   
> I think `Z` is more important than other measures. Another issue is that if BG advertises support of other measures, then users will likely expect e.g. why `M` is not copied/preserved/synthesised etc. by operation X :-)  
  
I am not fluent with GIS applications but `M` seems to me important as well. In any case why not reading an `M` coordinate and then discard it in geometric operations? For example, we could have a utility that tell the user if the point has an M coordinate or not (by convention this could be always the last coordinate). The rest of the coordinates (1, 2, 3 or more) will be considered "spatial".
