# #238 Add dynamic_step.hpp for base templates declaration [Merged]

> Username: mloskot  
> Created at: 2019-02-19 10:04:21 UTC  
> Updated at: 2019-03-29 20:27:05 UTC  
> Merged at: 2019-02-19 16:34:21 UTC  
> Closed at: 2019-02-19 16:34:21 UTC  
> Url: https://github.com/boostorg/gil/pull/238  

Replace all scattered around forward declarations of  
`dynamic_{x,y,xy}_step_type` with this common header,  
where the templates are also documented.  
  
Refine documentation of `transposed_type` - this is kept  
in `locator.hpp` as it is not just related to the dynamic step.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2019-02-19 13:28:05 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/238#pullrequestreview-205208969  

Nice !

---
