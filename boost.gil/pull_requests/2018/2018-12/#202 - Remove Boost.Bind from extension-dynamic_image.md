# #202 Remove Boost.Bind from extension/dynamic_image [Merged]

> Username: mloskot  
> Created at: 2018-12-18 01:31:38 UTC  
> Updated at: 2018-12-20 15:52:25 UTC  
> Merged at: 2018-12-20 15:51:21 UTC  
> Closed at: 2018-12-20 15:51:21 UTC  
> Url: https://github.com/boostorg/gil/pull/202  

Replace `boost::bind` with `std::bind` from C++11.  
Move template parameter comments to Doxygen `\tparam`-s, so they are captured as proper documentation.  
Tidy up code formatting.  
  
### References  
  
- #203  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-12-19 00:17:56 UTC  
> Url: https://github.com/boostorg/gil/pull/202#issuecomment-448421422  

AppVeyor is green  
Travis for my fork is green https://travis-ci.org/mloskot/gil/builds/469749856  
Travis for boostorg is waiting  
  
@stefanseefeld Any objections to this PR or can I merge as soon as all CI-s are green?

---
