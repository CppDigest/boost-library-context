# #69 - boost::polygon::detail::resize() gives wrong result when expanding a shape [Closed]

> Username: Elmi77  
> Created at: 2021-11-05 08:26:43 UTC  
> Updated at: 2021-11-09 11:36:53 UTC  
> Closed at: 2021-11-09 11:36:53 UTC  
> Url: https://github.com/boostorg/polygon/issues/69  

I have some polygons which are expanded by using function boost::polygon::detail::resize(). What I would expect to get is a result where the new outline of the shape is bigger than the old one and ecause of that never touches the new one:  
  
[https://i.ibb.co/BqDKGtd/clip-ok.png](https://i.ibb.co/BqDKGtd/clip-ok.png)  
  
Here the green oultine is the original while the yellow one is the expected result.  
  
But what I get when I use the related boost function via a call to   
  
`boost::polygon::polygon_set_data<int> result;`  
`result.resize();`  
  
is something like that:  
  
[https://i.ibb.co/ftPNTCV/clip-boost.png](https://i.ibb.co/ftPNTCV/clip-boost.png)  
  
As one can see, the expanded shape follows the original one way too much and therefore results in a new shape, which overlaps the old one at some points.

---

## Comment 1

> Username: Elmi77  
> Created at: 2021-11-09 11:36:53 UTC  
> Url: https://github.com/boostorg/polygon/issues/69#issuecomment-964068810  

Duplicate of https://github.com/boostorg/polygon/issues/70
