# #875 - polygon difference returns self intersecting result (or is wrongly detecting it as such) [Closed]

> Username: jessesna  
> Created at: 2021-06-30 15:50:38 UTC  
> Updated at: 2021-07-28 12:01:24 UTC  
> Closed at: 2021-07-28 12:00:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/875  

I found at least one occasion where the result of a substraction is detected as self intersecting.  
  
The following example fails:  
  
I'm trying to subtract the white from the red polygon:  
  
![image](https://user-images.githubusercontent.com/78883245/123988644-7ab06800-d9c8-11eb-8d95-0d64dd185cda.png)  
  
The result is the green dashed polygon which looks fine  
  
![image](https://user-images.githubusercontent.com/78883245/123988673-813edf80-d9c8-11eb-86f9-1beda2d9adc4.png)  
  
A closer view of the interesting part  
  
![image](https://user-images.githubusercontent.com/78883245/123988708-88fe8400-d9c8-11eb-8e25-bbdca78fe498.png)  
  
is here:  
  
![image](https://user-images.githubusercontent.com/78883245/123988755-90259200-d9c8-11eb-8d91-d863953571fc.png)  
  
When zooming in to the marked corner  
  
![image](https://user-images.githubusercontent.com/78883245/123988788-974ca000-d9c8-11eb-818a-5e7781c042b9.png)  
  
(while admittedly being somewhat limited by the floating point accuracy of the viewer)  
  
it appears that there are two very close points which might or might not overlap  
  
![image](https://user-images.githubusercontent.com/78883245/123988830-a2073500-d9c8-11eb-9636-b9fdb5494a4a.png)  
  
(i didn't do the calculations; the main point is that boost.geometry thinks they overlap)  
  
Here's [a godbolt showing the behavior](https://godbolt.org/z/5x4sc94Eq).  
  
The coordinates of the four line segments forming the Z in the result polygon are  
  
```  
-38.166710648232516689, -29.97044076186023176  
-46.093710179049615761, -23.318898379209066718 // these two points are notably  
-46.093707539928615802, -23.318900593694593226 // but not awfully close  
-46.499997777166534263, -22.977982153068655435  
```  
While the differences are somewhat after the decimal point, it feels like they should be still more than big enough to not cause floating point accuracy problems.  
  
  
[There's a entry on SO for this issue](https://stackoverflow.com/questions/67904576/boost-geometry-polygon-difference-returns-self-intersecting-result-or-is-wrongl).  
  
I think it's related to issue #876  .

---

## Comment 1

> Username: kleunen  
> Created at: 2021-07-03 18:01:18 UTC  
> Updated at: 2021-07-03 18:07:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/875#issuecomment-873446207  

If you define:  
```  
#define BOOST_GEOMETRY_NO_ROBUSTNESS  
````  
  
It runs correctly:  
https://godbolt.org/z/saorEdz7z  
  
I believe in current version of boost geometry, this is the default behaviour  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/core/config.hpp#L30-L34

---

## Comment 2

> Username: jessesna  
> Created at: 2021-07-05 08:02:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/875#issuecomment-873895218  

I found an example which results in a self intersecting polygon with but not without the define:  
  
https://github.com/boostorg/geometry/issues/876#issuecomment-873894196

---

## Comment 3

> Username: barendgehrels  
> Created at: 2021-07-28 12:00:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/875#issuecomment-888251740  

Thanks for the report.  
  
The define `BOOST_GEOMETRY_NO_ROBUSTNESS` will be the default from (hopefully) version 1.78 on.  
Therefore I close this issue.  
  
But will add this case (and #876, which runs fine in newest Boost) to our testsuite to avoid regressions.
