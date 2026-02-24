# #661 - Complicated donut structures [Closed]

> Username: AndrewAtAvenza  
> Created at: 2020-01-20 18:37:24 UTC  
> Updated at: 2020-01-20 22:09:39 UTC  
> Closed at: 2020-01-20 22:09:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/661  

I'm trying to write a geometry conversion tool to go from my application's (Adobe Illustrator) geometries to boost::geometry's model structures. It's mostly gone fine, but I run into the donut case. Or rather, the nested donut: a shell with a hole, who in turn has a child (non-hole?). I _think_ the proper way to handle this is to 'start over' as soon as a hole has a child, treating any hole's child as a top-level polygon and then combining it with the original donut polygon as a multi_polygon. I think this still works with anything in boost::geometry that I throw at it, but I could be missing something.  
  
If I'm wrong, I'd love if someone could explain how I should be handling a multi-level donut geometry (for lack of a better term). If I am doing the right thing, I think this should be described in the documentation somewhere. Maybe on polygon?

---

## Comment 1

> Username: awulkiew  
> Created at: 2020-01-20 19:58:52 UTC  
> Updated at: 2020-01-20 20:01:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/661#issuecomment-576412244  

Do I understand correctly that you have a polygon with a hole and inside this hole there is another polygon? Maybe you could share a picture?  
  
In general a geometry such as this should be represented as multi-polygon containing a polygon with a hole and another polygon inside this hole. Have in mind that this second polygon cannot have exactly the same points as a hole (so exactly fitting the hole). Otherwise this is not valid multi-polygon. It can have some of the points common though. For example:  
  
`MULTIPOLYGON(((0 0, 5 0, 5 5, 0 5, 0 0),(0 0, 1 4, 4 4, 4 1, 0 0)),(0 0, 1 3, 3 3, 3 1, 0 0))`  
  
You can find the definitions we are using here: https://www.opengeospatial.org/standards/sfa  
Polygon is defined at page 26 and multi-polygon at page 31.

---

## Comment 2

> Username: AndrewAtAvenza  
> Created at: 2020-01-20 22:09:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/661#issuecomment-576444706  

It's funny, I was looking for that link the other day & I couldn't remember the title to find it!  
  
Yes, that's exactly what I'm talking about: "polygon with a hole and inside this hole there is another polygon". I see that they have a good example of my case in the MultiPolygon example diagrams (_figure 17d_). So that plus what you've already outlined is the confirmation I was hoping for. I had come around to thinking that your suggestion was the only way it could work, and I couldn't think of any reason why that _wouldn't_ work, but I worried that I had missed something. So I definitely appreciate the confirmation.  
  
I'll grant this is a kind of edge case, but as someone who deals with inputs from all GIS sources, I strive to cover even the dumbest of cases. I just don't trust of any of the GIS programs not to produce something as ludicrous as four or five level nested donut. At least now I know how to handle it.  
  
I do think it wouldn't hurt to stick a line in somewhere in the boost::geometry::polygon documentation about this, even it's just a hint to go read the MultiPolygon definition in the SFA document. I did a fair bit of searching on this trying to find some clue as to how I was meant to handle it and didn't find anything helpful.
