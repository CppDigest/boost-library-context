# #497 - `simplify` returns a closed polygon even when open polygons are configured [Open]

> Username: jpieper-tri  
> Created at: 2018-06-28 15:58:31 UTC  
> Updated at: 2018-08-09 13:00:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/497  

As of da16ae8e793d70ed754a9b40bd48135b96663ede by @barendgehrels  running `boost::geometry::simplify` returns closed polygons even when open polygons are requested.  
  
```  
typedef bg::model::polygon<Eigen::Vector2d, true, false> BPolygon;  
  
BPolygon work{{{-0.5, -0.5}, {-0.5, 0.5}, {50.5, 0.5}, {50.5, -0.5}}};  
  
BPolygon result;  
bg::simplify(work, result, 0.05);  
  
for (const auto& point : result.outer()) {  
  std::cout << point.x() << ", " << point.y() << "\n";  
}  
```  
  
Pre that revision, the output is:  
  
```  
-0.5, -0.5  
-0.5, 0.5  
50.5, 0.5  
50.5, -0.5  
```  
  
After, a closed polygon is instead returned:  
  
```  
50.5, 0.5  
50.5, -0.5  
-0.5, -0.5  
-0.5, 0.5  
50.5, 0.5  
```

---

## Comment 1

> Username: eyal0  
> Created at: 2018-08-09 13:00:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/497#issuecomment-411749144  

I noticed an issue with some code that I had: I gave boost a vector of pairs to simplify and it gave me back something way off.  I was able to solve the problem by giving simplify a linestring instead.  
  
I have a hunch that boost::geometry::simplify is looking at the type or the values and trying to figure out if the shape is closed or open.  
  
If you use a boost::model::linestring instead, does it work?
