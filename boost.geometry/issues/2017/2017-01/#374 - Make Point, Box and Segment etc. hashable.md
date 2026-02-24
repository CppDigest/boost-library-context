# #374 - Make Point, Box and Segment etc. hashable [Closed]

> Username: cirosantilli  
> Created at: 2017-01-07 15:48:35 UTC  
> Updated at: 2017-03-14 18:20:03 UTC  
> Closed at: 2017-03-14 18:20:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/374  

For insertion into unordered sets / maps.  
  
Possibly using `boost::hash_combine`.  
  
Any reason not to?

---

## Comment 1

> Username: awulkiew  
> Created at: 2017-01-07 23:45:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/374#issuecomment-271118372  

Typically one would like to perform spatial, knn, skyline, ray etc. query on a collection of geometries, so a query "geometrical" in nature. I'm not sure if it would have sense to search for geometries having exactly the coordinates you're looking for. For example, two Points are considered spatially equal if their coordinates are "close enough" to each other (wrt some epsilon, at least when the coordinates are floating point numbers). If they were stored in unordered set you'd be able to find only points having exactly the same coordinates. There could be other points spatially equal to them in the container but their corresponding hash values would be dfferent.  
  
Maybe with integer coordinates it'd have more sense but in general case I don't see a reason. Do you have some specific use case in mind?

---

## Comment 2

> Username: cirosantilli  
> Created at: 2017-01-08 00:03:33 UTC  
> Updated at: 2017-01-08 00:04:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/374#issuecomment-271119227  

l don't have a real world use case right now, I was just trying to do some unit tests to learn the API, and since I don't know in which order rtree query results will come out, I wanted to write:  
  
    assert(unordered_set(query results) == unordered_set({ expected }))  
  
If I come across an use case (I'm playing with integer coordinates in a 2D tile based game) I will ping back.
