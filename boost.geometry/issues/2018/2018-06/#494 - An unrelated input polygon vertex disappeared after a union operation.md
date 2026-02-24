# #494 - An unrelated input polygon vertex disappeared after a union operation [Open]

> Username: mcquay239  
> Created at: 2018-06-21 11:54:10 UTC  
> Updated at: 2018-06-21 11:54:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/494  

When uniting the following two polygons  
A: `POLYGON((-8 0,-10 0,-10 4,10 4,10 0,8 0,4 0,0 0,-8 0))`  
B: `POLYGON((0 2,4 2,4 0,4 -2,0 -2,0 0,0 2))`  
  
The union result is  
`MULTIPOLYGON(((4 -2,0 -2,0 0,-8 0,-10 0,-10 4,10 4,10 0,4 0,4 -2)))`  
  
But the expected result is  
`MULTIPOLYGON(((4 -2,0 -2,0 0,-8 0,-10 0,-10 4,10 4,10 0,8 0,4 0,4 -2)))`  
  
The vertex `POINT(8 0)` was erased by a call to `detail::overlay::append_no_collinear` inside of `travel_to_next_turn`. The expectation is that any input vertex lying on the result boundary must be a result vertex.  
  
AFAIU, the problem was introduced in d0ca69a058. And according to the commit message the expected invariant holds.  
  
![image](https://user-images.githubusercontent.com/812812/41717552-c2cc6a80-7562-11e8-95ee-0d5de4ad839d.png)
