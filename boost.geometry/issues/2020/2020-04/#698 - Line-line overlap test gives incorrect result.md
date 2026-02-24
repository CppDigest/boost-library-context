# #698 - Line-line overlap test gives incorrect result [Closed]

> Username: lievenvanderheide  
> Created at: 2020-04-18 21:27:39 UTC  
> Updated at: 2020-04-19 03:10:58 UTC  
> Closed at: 2020-04-18 22:17:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/698  

The two line strings in the following code clearly overlap, yet bg::overlaps returns false:  
  
`  
	namespace bg = boost::geometry;  
  
	using point_t = bg::model::point<double, 2, bg::cs::cartesian>;  
	using linestring_t = bg::model::linestring<point_t>;  
  
	linestring_t a =   
	{  
		point_t(30, 160),  
		point_t(100, 180),  
		point_t(110, 210),  
	};`  
  
	linestring_t b =   
	{  
		point_t(85, 185),  
		point_t(105, 190)  
	};  
  
	if (bg::overlaps(a, b))  
	{  
		std::cout << "Yay!" << std::endl;  
	}  
	else  
	{  
		std::cout << "Nay!" << std::endl;  
	}  
`

---

## Comment 1

> Username: tinko92  
> Created at: 2020-04-18 21:49:53 UTC  
> Updated at: 2020-04-18 21:53:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/698#issuecomment-615955989  

I took your example and made an image of it with the svg_mapper. It looks like this:  
![no_overlap](https://user-images.githubusercontent.com/17624459/79672045-b51c5380-81ce-11ea-8544-eaba8256ecf7.png)  
I don't think that constitutes an overlap in the sense of the OGC Simple Feature Specification ( http://www.opengeospatial.org/standards/sfa ) because the intersection of the interiors of a and b is a point rather than a line. I think the relationship between those two linestrings would be bg::crosses which returns true.  
  
I suppose, you would have an example for overlaps if you instead chose b like:  
```  
linestring_t b =   
{  
        point_t(65, 170),  
        point_t(135, 190)  
};  
```  
which would look like this:  
![overlaps](https://user-images.githubusercontent.com/17624459/79672157-cdd93900-81cf-11ea-873c-dbe359d7471a.png)

---

## Comment 2

> Username: lievenvanderheide  
> Created at: 2020-04-18 22:01:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/698#issuecomment-615958563  

Ok, in that case bg::crosses is indeed what I'm interested in.  
  
I think this is something which really should be added to the documentation, because if not stated otherwise, then I'm pretty sure most people would assume that two geometries overlap if they share at least one point.

---

## Comment 3

> Username: awulkiew  
> Created at: 2020-04-19 03:06:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/698#issuecomment-616015657  

Or maybe do you have `intersects` in mind?  
Also have a look at this: https://en.wikipedia.org/wiki/DE-9IM
