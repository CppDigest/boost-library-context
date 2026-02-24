# #45 - voronoi_diagram provides no non-const acccessors to cells, edges and vertices [Closed]

> Username: bubnikv  
> Created at: 2020-06-18 12:41:35 UTC  
> Updated at: 2020-06-25 14:14:14 UTC  
> Closed at: 2020-06-25 14:14:14 UTC  
> Url: https://github.com/boostorg/polygon/issues/45  

voronoi_diagram provides const methods cells(), edges() and vertices(). Having no way to access non-const variants of these members makes impossible to set color of cells, edges or vertices without resorting to const_cast.  
  
Please either provide non-const accessors.   
  
I understand that it may not be a good idea to give non-const access to these voronoi_diagram elements. Alternatively, possibly a better solution would be to add set_color() methods to voronoi_diagram to possibly encapsulate the const_cast.

---

## Comment 1

> Username: bubnikv  
> Created at: 2020-06-25 14:14:14 UTC  
> Url: https://github.com/boostorg/polygon/issues/45#issuecomment-649571262  

I see the color_ member on vertices, edges and cells are marked mutable, and the color setters are const, therefore it actually IS possible to set the color, therefore my complaint is not valid. I have never seen such an approach though, it is certainly surprising.
