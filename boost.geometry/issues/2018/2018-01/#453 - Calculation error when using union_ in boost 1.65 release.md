# #453 - Calculation error when using union_ in boost 1.65 release [Closed]

> Username: huangynn  
> Created at: 2018-01-22 06:22:40 UTC  
> Updated at: 2018-02-23 07:17:03 UTC  
> Closed at: 2018-02-08 17:27:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/453  

A polygon num: 1210  
A Area is 0.0491783  
B polygon num: 3201  
B Area is 0.0594317 (using bg::area())  
calculate A+B=C (using union_(A,B,C))  
  
C polygon num 2143  
Output value :C Area is 0.0201694  
  
why the area of union of two multi_polygon is the smallest among all value?

---

## Comment 1

> Username: huangynn  
> Created at: 2018-01-23 06:28:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-359689336  

![image](https://user-images.githubusercontent.com/12636388/35261064-7a0f5106-0049-11e8-9f6b-ed85fefce9b1.png)  
  
green is A  
purple is B  
And brown is C  
  
Very interest , can anyone help me to figure this out?

---

## Comment 2

> Username: barendgehrels  
> Created at: 2018-01-23 07:18:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-359698039  

Apparently some output is missed indeed.  
  
1) can you please try it with Boost 1.66? There are improvements in union. (And even after 1.66 there are again improvements, to be released)  
  
2) if that does not help, can you provide the Wkt's of A and B?

---

## Comment 3

> Username: huangynn  
> Created at: 2018-01-23 09:14:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-359727463  

It's not just missing, absolutely something not exist in A or B appears in C.  
I'll try 1.66

---

## Comment 4

> Username: huangynn  
> Created at: 2018-01-23 12:49:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-359780488  

[crop1.txt](https://github.com/boostorg/geometry/files/1655923/crop1.txt)  
[crop2.txt](https://github.com/boostorg/geometry/files/1655929/crop2.txt)  
  
A&B as above  
  
1.66 makes no difference  
![image](https://user-images.githubusercontent.com/12636388/35276295-eefed1b0-007d-11e8-826d-bbbf666248f8.png)

---

## Comment 5

> Username: huangynn  
> Created at: 2018-01-23 13:46:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-359795025  

By the way , I use cgal to correct all invalid polygons, and bg::is_valid to check all generated polygons, no invalid polygon in A or B

---

## Comment 6

> Username: huangynn  
> Created at: 2018-01-31 06:39:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-361838787  

Any update?

---

## Comment 7

> Username: barendgehrels  
> Created at: 2018-01-31 09:34:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-361875462  

Thanks for trying 1.66 and sending the WKT's.  
  
These are WKT's of polygons, one contains >1000 polygons, the other >3000  
But I cannot find the labels you mention. If you refer to line-numbers, they would be both the last ones, but then I get different results (polygons do not even overlap).  
  
Can you please attach only the WKT of the polygon A and the polygon B, (so the ones with the areas you mention) and not all of them?  
  
Or, if the two files you attached are multi-polygons and not polygons, how did you do the union then? Because we only support multipolygon and not a collection of polygons.   
So, in this case, can you then attach them as two, valid, multipolygons (the polygons themselves might be valid, but their combination is not).

---

## Comment 8

> Username: huangynn  
> Created at: 2018-01-31 10:38:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-361892721  

I composite a multipolygon using polygons. The polygons in one file do not overlap.

---

## Comment 9

> Username: barendgehrels  
> Created at: 2018-01-31 11:31:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-361905429  

I see. But please send me two WKT's of the multipolygons.  
Of course I could repeat your program and composing all manually, but you have it already - otherwise you would not be able to do the union.

---

## Comment 10

> Username: huangynn  
> Created at: 2018-01-31 11:47:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-361909092  

I use the same input of sended files below... The csv file have two columns, the first column is standard wkt

---

## Comment 11

> Username: barendgehrels  
> Created at: 2018-01-31 14:47:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-361954152  

If you send me two WKT 's of multipolygon A and B, right before union, I'll be happy to help you.  
std::cout << std::setprecision(20) << bg::wkt(A) << std::endl;  
std::cout << std::setprecision(20) << bg::wkt(B) << std::endl;  
// union_(A,B,C)

---

## Comment 12

> Username: barendgehrels  
> Created at: 2018-02-06 17:18:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-363496671  

Any update?

---

## Comment 13

> Username: huangynn  
> Created at: 2018-02-08 02:19:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-363980792  

I've already find another way to do this, I think for thousands of polygon logic calculation, use geometry lib maybe not the best choice.

---

## Comment 14

> Username: barendgehrels  
> Created at: 2018-02-08 17:27:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-364186467  

OK, your choice.

---

## Comment 15

> Username: awulkiew  
> Created at: 2018-02-08 22:33:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-364271667  

@huangynn I don't know what order and closure was used in your case. I used the defaults (CW, closed) and called correct for each polygon before storing it in multi-polygon. I also use develop branch, not 1.66. This may be the reason why for multi-polygon C I get:  
  
C polygon num : 1939  
C area: 0.0186469  
  
@barendgehrels If you want to test by yourself:  
  
[mpoly1.txt](https://github.com/boostorg/geometry/files/1709048/mpoly1.txt)  
[mpoly2.txt](https://github.com/boostorg/geometry/files/1709052/mpoly2.txt)  
  
But anyway, the multipolygons created from these polygons are detected as invalid:  
  
    A: Geometry has invalid self-intersections. A self-intersection point was found at (128.25, 42.7349); method: m; operations: i/u; segment IDs {source, multi, ring, segment}: {0, 519, -1, 156}/{0, 1107, -1, 23}  
    B: Geometry has invalid self-intersections. A self-intersection point was found at (128.25, 42.9491); method: i; operations: i/u; segment IDs {source, multi, ring, segment}: {0, 2758, -1, 82}/{0, 685, -1, 252}  
  
The above means for example that in multi-polygon B segment `82` of exterior ring (`-1`) of polygon `2758` intersects with segment `252` of exterior ring (`-1`) of polygon `685`. And indeed they look like this (`2758` is red, `685` is green):  
  
![obraz](https://user-images.githubusercontent.com/1226951/36001729-ae9479e8-0d27-11e8-82ce-1a2757b5126f.png)  
  
Geometries have to be valid before they are passed into union algorithm.

---

## Comment 16

> Username: awulkiew  
> Created at: 2018-02-09 00:40:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-364297619  

There are many polygons causing problems (intersecting or sharing edge). In order to generate valid multipolygon they should be merged (e.g. by running `union_` for each one of them). If you want to know how MultiPolygon's validity is defined read [OpenGIS Implementation Specification for Geographic information - Simple feature access - Part 1: Common architecture](http://www.opengeospatial.org/standards/sfa) p. 31 pt. 6.1.14.

---

## Comment 17

> Username: barendgehrels  
> Created at: 2018-02-09 17:34:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-364503158  

@awulkiew Thanks for your input! I suspected that the multi-polygon(s) would be invalid because apparently they were just combined from polygons. Indeed they should be merged by union_ calls  
  
If I'm finishing the current action on rescaling I will probably test this, it is an interesting testset.

---

## Comment 18

> Username: huangynn  
> Created at: 2018-02-23 07:17:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/453#issuecomment-367929704  

Thank all of you for helping me out. I've tested every single polygon, but not the multi polygon, I think you got it right.  I use opencv to find all single polygons from a mask , so , they should not share edge. I think this situation occurs when I transfer the coords of polygons due to loss of significance
