# #87 Add move in voronoi_diagram with C++11 [Open]

> Username: awulkiew  
> Created at: 2023-09-25 11:51:43 UTC  
> Updated at: 2023-09-25 11:51:43 UTC  
> Url: https://github.com/boostorg/polygon/pull/87  

Add default move constructor and assignment operator if neither BOOST_NO_CXX11_RVALUE_REFERENCES nor BOOST_NO_CXX11_DEFAULTED_FUNCTIONS are defined.  
  
Copying of voronoi_diagram is disabled because elements hold pointers which would be invalid after copying. But this would not be the case after move (allocators used by vectors doesn't change).

---
