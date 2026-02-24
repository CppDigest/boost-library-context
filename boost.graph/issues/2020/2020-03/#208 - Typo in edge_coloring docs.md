# #208 - Typo in edge_coloring docs [Closed]

> Username: krashish8  
> Created at: 2020-03-13 06:34:47 UTC  
> Updated at: 2021-12-09 06:26:39 UTC  
> Closed at: 2021-12-09 06:26:39 UTC  
> Url: https://github.com/boostorg/graph/issues/208  

It appears that the basic template of the docs of `edge_coloring` was taken from the docs of `king_ordering` and modified accordingly. However, some parts of the docs still contain words from `king_ordering` documentation, which must also be modified.  
  
The following changes must be done:  
  
In the **Example** section of the docs, the line `See example/king_ordering.cpp` shall be changed to `See example/edge_coloring.cpp`. The hypertext reference of the link is correct, though.  
  
  
Also, there is the following comment in the HTML file of the docs:  
  
`<!-- King, I.P. An automatic reordering scheme for simultaneous equations derived from network analysis. Int. J. Numer. Methods Engrg. 2 (1970), 523-533 -->`  
  
which is also copied from `king_ordering`. It should be replaced with the following comment:  
  
`<!-- Misra, J., & Gries, D. (1992). A constructive proof of Vizing's theorem. In Information Processing Letters. -->`  
  
The reference of this can be found [here](https://github.com/boostorg/graph/blob/ca17604dca362e0378ca14c7e8cf9a57a0c887ad/include/boost/graph/edge_coloring.hpp#L23).
