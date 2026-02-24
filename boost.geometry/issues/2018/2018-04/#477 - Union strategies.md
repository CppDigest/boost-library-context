# #477 - Union strategies [Closed]

> Username: ghost  
> Created at: 2018-04-21 17:08:46 UTC  
> Updated at: 2024-10-02 12:23:28 UTC  
> Closed at: 2024-10-02 12:23:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/477  

Hello,  
  
I want to merge two polygon that should have one or several segment in common (or at least they should be very near). But they will never completely overlap. I tried union_ with the default strategy without any success, and didn't find anything about Union_ Strategy Concept. So I'm wondering how I should do that? If it's possible.  
  
Thanks

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-04-22 19:06:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/477#issuecomment-383404703  

Hi,  
  
Union should merge them unless they were close but not overlapping, then both polygons should be in the output. Could you share the case which doesn't work for you? Have in mind that the orientation and closure of Polygons has to be correct (so the input has to be valid) and that the the output of `union_()` has to be MultiPolygon.

---

## Comment 2

> Username: ghost  
> Created at: 2018-04-25 20:16:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/477#issuecomment-384419858  

Sorry it's a bit hard to answer these days. So I wanted to show you the values, and them I tried again with a small test. And it was working... I just realized that given the `.data()` of a `std::vector<point_2d> `to assign_points wasn't a good idea... Anyway that doesn't answer my real question which is : is there any Union Strategy to apply a radius about if the polygon overlap or not? Where I can find the documentation about Union Strategies?

---

## Comment 3

> Username: vissarion  
> Created at: 2024-10-02 12:23:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/477#issuecomment-2388511696  

Union strategies are undocumented (as most of the strategies).   
  
The question it is not very clear to me, what I understand is that the user want to merge polygons only in the case they share one (or multiple edges), in other words their intersection is a set of linestrings and not polygons. It is very old but just for the history I am moving it to discussions.
