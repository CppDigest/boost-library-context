# #47 - voronoi generator could create empty cells [Open]

> Username: bubnikv  
> Created at: 2020-06-22 15:27:49 UTC  
> Updated at: 2021-06-20 13:52:32 UTC  
> Url: https://github.com/boostorg/polygon/issues/47  

The voronoi builder calls voronoi_diagram::_build()  
to remove degenerate edges and vertices. Once this is done, Voronoi cells are sometimes left, which reference no edge. These invalid cells should be removed as well by the same method.

---

## Comment 1

> Username: bubnikv  
> Created at: 2020-06-23 06:26:18 UTC  
> Url: https://github.com/boostorg/polygon/issues/47#issuecomment-647936455  

There are methods:  
voronoi_cell::is_degenerate()  
voronoi_vertex::is_degenerate()  
  
At least it should be documented, that the Voronoi generator could create degenerate cells.

---

## Comment 2

> Username: eadf  
> Created at: 2021-06-20 13:52:32 UTC  
> Url: https://github.com/boostorg/polygon/issues/47#issuecomment-864558671  

It does indeed look like this *could* happen.   
```  
void _build() {  
      ...  
      // Update prev/next pointers for the ray edges.  
      for (cell_iterator cell_it = cells_.begin();  
         cell_it != cells_.end(); ++cell_it) {  
         if (cell_it->is_degenerate())  
            continue;   
```  
But I've never seen it happen, do you have any example?
