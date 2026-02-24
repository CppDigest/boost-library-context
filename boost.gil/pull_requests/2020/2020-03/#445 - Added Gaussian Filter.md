# #445 [WIP] Added Gaussian Filter [Closed]

> Username: laxsuryavanshi  
> Created at: 2020-03-08 17:08:12 UTC  
> Updated at: 2020-03-08 17:39:35 UTC  
> Closed at: 2020-03-08 17:28:04 UTC  
> Url: https://github.com/boostorg/gil/pull/445  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
A Gaussian filter is a linear filter. It's usually used to blur the image or to reduce Gaussian noise. Current implementation relies on boost::gil functions. Builtin convolve_2d can only use convolve_option_extend_zero as convolve_boundary_option. In the future, the implementation will be improved by using the Gaussian filter's property of being separable.  
  
### References  
  
https://homepages.inf.ed.ac.uk/rbf/HIPR2/gsmooth.htm  
https://en.wikipedia.org/wiki/Gaussian_blur  
  
### Tasklist  
  
- [ ] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve  
- [ ] Improve algorithm  
- [ ] Add more de-noising algorithm

---
