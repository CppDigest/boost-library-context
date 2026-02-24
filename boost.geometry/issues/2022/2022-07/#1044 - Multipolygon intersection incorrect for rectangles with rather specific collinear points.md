# #1044 - Multipolygon intersection incorrect for rectangles with rather specific collinear points [Open]

> Username: thmshnz  
> Created at: 2022-07-25 09:53:10 UTC  
> Updated at: 2024-11-16 10:43:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/1044  

The source files in the attached archive [bug.zip](https://github.com/boostorg/geometry/files/9179917/bug.zip) demonstrate an intersection bug that is present in version 1.79.0. Given two intersecting rectangles each with collinear points represented as two multi_polygons, for specific choices of those points, `boost::geometry::intersection` produces an incorrect result.  
  
nonbug.cpp produces the following output where the second part of the output refers to a case with the y-coordinates of the points on the second rectangle being perturbed ever so slightly. As expected, the output is the same in both cases.  
```  
Valid (mpoly1): 1  
Valid (mpoly2): 1  
Valid (mpoly3): 1  
Area:           0.09  
Bounding box:   ((-5.8, 5), (-5.5, 5.3))  
  
Valid (mpoly1): 1  
Valid (mpoly2): 1  
Valid (mpoly3): 1  
Area:           0.09  
Bounding box:   ((-5.8, 5), (-5.5, 5.3))  
```  
  
However, bug.cpp produces the following output where the first part indicates the bug. After perturbing the y-coordinates of the points on the second rectangle, the problem vanishes.  
  
```  
Valid (mpoly1): 1  
Valid (mpoly2): 1  
Valid (mpoly3): 1  
Area:           0.24  
Bounding box:   ((-5.8, 5), (-5, 5.3))  
  
Valid (mpoly1): 1  
Valid (mpoly2): 1  
Valid (mpoly3): 1  
Area:           0.09  
Bounding box:   ((-5.8, 5), (-5.5, 5.3))  
```  
  
Note that the difference between both files is as follows.  
```  
--- nonbug.cpp  2022-07-21 13:00:55.168046300 +0200  
+++ bug.cpp     2022-07-25 11:00:54.132520800 +0200  
@@ -165,8 +165,8 @@ double r2[][2] = {  
     {-5.54, 5.3},  
     {-5.53, 5.3},  
     {-5.52, 5.3},  
-    {-5.51, 5.3},  
-    {-5.5, 5.3},  
+    {-5.51, 5.299999999999998},  
+    {-5.5, 5.299999999999998},  
     {-5.49, 5.3},  
     {-5.48, 5.3},  
     {-5.47, 5.3},  
```

---

## Comment 1

> Username: barendgehrels  
> Created at: 2022-07-27 20:54:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/1044#issuecomment-1197356944  

Thanks for the report! I'll have a look next week.

---

## Comment 2

> Username: thmshnz  
> Created at: 2022-08-11 13:38:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1044#issuecomment-1212003227  

Looking at boost versions 1.76 - 1.80, I noted that the bug is present in version 1.79 and the latest 1.80 release. It is not present in versions 1.76 - 1.78.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2022-08-17 09:33:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/1044#issuecomment-1217761536  

Sorry, it's a few weeks later already.  
It's a numerical issue. Using `long double` fixes this particular example.  
Boost 1.79 is different from the versions before, because we stopped using rescaling (which caused several other issues).  
I will look later if this issue can be fixed in a generic way, but that will not be soon.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2024-11-16 10:43:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/1044#issuecomment-2480519449  

Not fixed on latest branch or on PR #1343
