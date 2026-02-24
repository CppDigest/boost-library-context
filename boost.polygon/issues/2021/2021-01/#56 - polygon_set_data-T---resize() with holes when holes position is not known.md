# #56 - polygon_set_data<T>::resize() with holes when holes position is not known? [Open]

> Username: Elmi77  
> Created at: 2021-01-26 08:22:06 UTC  
> Updated at: 2021-01-29 16:39:00 UTC  
> Url: https://github.com/boostorg/polygon/issues/56  

Hi,  
  
I have a set of polygon data which consist of several arrays of coordinates which form the polygons. I resize them with polygon_set_data<T>::resize() using boost::polygon::polygon_with_holes_data. Unfortunately there is a problem:  
  
boost::polygon::polygon_with_holes_data expects a clear data structure: an outline polygon in one part of the structure and the polygons in a different one. But my input coordinate arrays do not make such definitions, they are just sets of polygons where it is known they belong together. So as an example: let's say I have two arrays of polygon data, one describes a triangle and one a rectangle. For these two shapes there is no clear definition which is the total outline and which is the hole. This definition is made implicitly by the size of these shapes and their location: so when the triangle is smaller than the rectangle and located completely within the rectangle, the triangle is the hole.  
  
So my question is: is there a way to resize() polygons without that clear outline/holes structure? Means when outline and hole are swapped or mixed? Or are there any functions in boost::polygon which give the possibility to sort my input data by outline and holes?  
  
Thanks!

---

## Comment 1

> Username: by408  
> Created at: 2021-01-29 02:08:28 UTC  
> Url: https://github.com/boostorg/polygon/issues/56#issuecomment-769524831  

You can create one polygon_with_holes_data with only a hole and another one without the hole.  
polygon_with_holes_data pol1; // add hole to this one  
polygon_with_holes_data pol2; // add outer ring to this one  
Then do:  
polygon_set_data poly_set;  
poly_set.insert(pol2);  
poly_set.insert(pol1, true); // true means hole  
  
vector<polygon_with_holes_data > output;  
poly_set.get(output).  
  
Now, output will have one polygon only with an outer ring and an inner ring. Get function will do proper merging for you. You can do this with overlapping polygons too, the get will take care of topology. The output of get is clean, then you can do resizing after that. I have not tested if you can do resizing before get. You can try.  
Note that some boost functions do not handle redundant/repeating points. Make sure you don't have duplicate or collinear points.

---

## Comment 2

> Username: Elmi77  
> Created at: 2021-01-29 09:32:06 UTC  
> Url: https://github.com/boostorg/polygon/issues/56#issuecomment-769690782  

Thanks but this is not a solution: your suggestion also assumes I know what the hole and what the outline is - but this isn't the case. I only have a bunch of polygons where one is the outline - but I don't know which one.  
  
That's why I'm looking for a possibility which does not need to separate outline and holes or where I can detect in advance which is which.

---

## Comment 3

> Username: by408  
> Created at: 2021-01-29 16:37:42 UTC  
> Updated at: 2021-01-29 16:39:00 UTC  
> Url: https://github.com/boostorg/polygon/issues/56#issuecomment-769913148  

You need to define what a hole means, boost does not do that, nobody can do that correctly. I could have a triangle and a rectangle, triangle inside the rectangle but I can assume they are just overlapping polygons, no holes. You want to assume it is a hole.  So you need to define what is a hole and detect it yourself. If holes are reversely oriented you can detect by area, if you want a polygon covered by another to be defined as a hole you can detect by calling boost::geometry::covered_by.  
In a typically application topology is well defined, so holes are inversely oriented.  
In my suggestions you do not need to know which polygon is outline of which. You just insert one polygon at a time not in any specific order, just tell which ones are holes. You don't need to know the covering polygons.
