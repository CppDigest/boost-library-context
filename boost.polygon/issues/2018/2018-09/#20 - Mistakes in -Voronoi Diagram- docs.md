# #20 - Mistakes in "Voronoi Diagram" docs [Closed]

> Username: artem-ogre  
> Created at: 2018-09-13 13:12:16 UTC  
> Updated at: 2018-09-17 09:01:43 UTC  
> Closed at: 2018-09-17 09:01:43 UTC  
> Url: https://github.com/boostorg/polygon/issues/20  

Hi Andrii,  
  
I would like to point out some mistakes in the documentation for Voronoi Vertex member function signatures (in file: **[polygon/doc/voronoi_diagram.htm](https://github.com/boostorg/polygon/blob/master/doc/voronoi_diagram.htm)** ):  
  
- `const point_type& x() const` should be `const coordinate_type& x() const`  
- `const point_type& y() const` should be `const coordinate_type& y() const`

---

## Comment 1

> Username: artem-ogre  
> Created at: 2018-09-13 13:23:55 UTC  
> Url: https://github.com/boostorg/polygon/issues/20#issuecomment-421005248  

I have created a pull request for this issue: #21

---

## Comment 2

> Username: artem-ogre  
> Created at: 2018-09-17 09:01:43 UTC  
> Url: https://github.com/boostorg/polygon/issues/20#issuecomment-421935222  

fixed with #21
