# #591 - "See also" links in documentation are broken [Closed]

> Username: schra  
> Created at: 2021-04-02 17:42:25 UTC  
> Updated at: 2022-05-03 14:10:04 UTC  
> Closed at: 2022-05-03 14:10:04 UTC  
> Url: https://github.com/boostorg/gil/issues/591  

### Actual behavior  
  
Go to a "See also" link (not sure if all of them are broken). For example:  
  
1. Go to https://boostorg.github.io/gil/html/design/point.html  
2. Click on PointNDConcept under the "See also" section  
3. Find a 404 page  
  
### Expected  behavior  
  
The documentation of the linked type

---

## Comment 1

> Username: mloskot  
> Created at: 2021-04-02 17:45:32 UTC  
> Url: https://github.com/boostorg/gil/issues/591#issuecomment-812636924  

Thanks for the report, it looks like the fix is to remove `design/` from the paths as in here:  
  
```  
- https://boostorg.github.io/gil/html/design/reference/structboost_1_1gil_1_1_point_n_d_concept.html  
+ https://boostorg.github.io/gil/html/reference/structboost_1_1gil_1_1_point_n_d_concept.html  
```  
  
```  
- https://boostorg.github.io/gil/html/design/reference/structboost_1_1gil_1_1_point2_d_concept.html  
+ https://boostorg.github.io/gil/html/reference/structboost_1_1gil_1_1_point2_d_concept.html  
```
