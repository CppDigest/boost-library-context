# #846 - Incorrect behavior of RTree with points on spherical coordinate [Closed]

> Username: cmpute  
> Created at: 2021-05-05 02:00:02 UTC  
> Updated at: 2021-06-03 14:19:06 UTC  
> Closed at: 2021-05-05 16:54:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/846  

I created a test program to test RTree with spherical coordinate system (see https://gist.github.com/cmpute/19a10ff01c736a433cb23430bb0399a1), but I found some unexpected behavior. RTree query will miss some data points in some cases. I cannot find any pattern of the problem. Either I misunderstood the behavior of `box` in spherical coordinate system, or there are some bugs in spherical coordinate system support of boost RTree.  
  
Parameters of some error cases are listed below:  
- 2D mode (without `#define USE3D` flag):  
  - `./test_spherical 2 2 0`  
  - `./test_spherical 2 2 1`  
- 3D mode (with `#define USE3D` flag):  
  - all cases above  
  - `./test_spherical 1 1 5` (no problem in 2D mode)  
  - `./test_spherical 1 1 1 1.7` (no problem when change `1.7` to `1.8`, which makes the range lower limit negative)  
  
Thanks for suggestions or fixes in advance!

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-05-05 14:38:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/846#issuecomment-832741984  

Thanks for the report!  
  
The sample code is quite complicated. Could you simplify it, pick one specific case and e.g. show a set of points that is stored in the R-tree, query box and the result? Directly in spherical CS, without conversion between ECEF and spherical.  
  
Regarding the spherical box. Min longitude has to be smaller or equal to max longitude. The longitude should be in [-180, 540) and the width should be in [0, 360]. Latitude should be in [-90, 90]. The edges of a box are defined by meridians and parallels.  
  
Regarding query, according to DE9IM (https://en.wikipedia.org/wiki/DE-9IM) for `within` interiors of both geometries must intersect, point has no boundary so if a point is on the boundary of a query box then the result is `false`. Typically we use `covered_by` or `intersects` with R-tree. I don't know if it does matter in your case, I'm just pointint it out.

---

## Comment 2

> Username: cmpute  
> Created at: 2021-05-05 15:06:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/846#issuecomment-832764535  

@awulkiew Thanks for the clarification! I will try to compose a smaller example. About the spherical box, should the latitude range be in [0, 180] in spherical CS? I thought [-90, 90] is the range for equatorial spherical CS. And besides, is there any limitation of box in 'range' direction? (i.e. elevation)

---

## Comment 3

> Username: awulkiew  
> Created at: 2021-05-05 15:19:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/846#issuecomment-832775152  

> About the spherical box, should the latitude range be in [0, 180] in spherical CS? I thought [-90, 90] is the range for equatorial spherical CS.  
  
Yes, you're correct. I was thinking about `spherical_equatorial`. Btw, does the problem exist also in equatorial?  
  
> And besides, is there any limitation of box in 'range' direction? (i.e. elevation)  
  
No, I don't think so. Maybe only that it should be >0 if you treat it as height/radius. The rest of the coordinates are treated the same as in cartesian so if a point falls in range no matter what it is then it's considered to be inside a box.

---

## Comment 4

> Username: cmpute  
> Created at: 2021-05-05 16:54:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/846#issuecomment-832852191  

Interesting... I found that the test program runs well on Ubuntu 20.04 (with boost 1.71), but fails on Ubuntu 18.04 (with boost 1.65). So I'm going to close this issue. @awulkiew Thanks for your help!

---

## Comment 5

> Username: awulkiew  
> Created at: 2021-05-06 19:21:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/846#issuecomment-833796846  

Indeed interesting. If you experience it in a different case feel free to reopen. Btw, I think it's safer to use `spherical_equatorial` because spherical (polar) is not that well tested.

---

## Comment 6

> Username: cmpute  
> Created at: 2021-05-08 02:47:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/846#issuecomment-834995297  

@awulkiew I tried the `spherical_equatorial` coordinate system and it seems there's not problem with that CS, thanks for the information!
