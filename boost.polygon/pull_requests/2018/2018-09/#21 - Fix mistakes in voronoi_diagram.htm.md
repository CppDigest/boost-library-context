# #21 Fix mistakes in voronoi_diagram.htm [Merged]

> Username: artem-ogre  
> Created at: 2018-09-13 13:22:51 UTC  
> Updated at: 2018-09-17 09:06:58 UTC  
> Merged at: 2018-09-16 10:00:40 UTC  
> Closed at: 2018-09-16 10:00:40 UTC  
> Url: https://github.com/boostorg/polygon/pull/21  

Fix signatures of two voronoi vertex methods:  
    - `const point_type& x() const` => `const coordinate_type& x() const`  
    - `const point_type& y() const` => `const coordinate_type& y() const`

---

## Comment 1

> Username: asydorchuk  
> Created_at: 2018-09-16 10:00:34 UTC  
> Url: https://github.com/boostorg/polygon/pull/21#issuecomment-421735767  

@artem-ogre thanks for the fix!

---
