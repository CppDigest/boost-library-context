# #469 Add example with anisotropic diffusion [Closed]

> Username: simmplecoder  
> Created at: 2020-03-29 12:12:30 UTC  
> Updated at: 2020-08-06 16:02:50 UTC  
> Closed at: 2020-08-06 16:02:50 UTC  
> Url: https://github.com/boostorg/gil/pull/469  

### Description  
  
This commit implements anisotropic diffusion as example program. The main difference from Gaussian smoothing is that it isn't a linear, as it adapts the scaling depending on the region surrounding the pixel. In general it seems to be more careful about the edges, and relatively easy to implement .  
  
This is my solution to a GSoC competency test.  
  
### References  
  
http://www.sci.utah.edu/~gerig/CS7960-S2010/materials/Perona-Malik/anisotropic_diffusion-Book.pdf  
  
### Tasklist  
  
- [X] Implement anisotropic diffusion  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-03-30 09:15:30 UTC  
> Updated_at: 2020-03-30 09:24:06 UTC  
> Url: https://github.com/boostorg/gil/pull/469#issuecomment-605881301  

Thanks for the example. For the competency test it looks good.  
  
For submission to GIL as example, it needs copyright notice, Jamfile/CMakeLists.txt update, some formatting (even minimal using the [.clang-format](https://github.com/boostorg/gil/tree/develop/example/clang-format)),  according to GIL guidelines. However, it will not be accepted before the GSoC students selection is completed.

---

## Comment 2

> Username: simmplecoder  
> Created_at: 2020-08-06 16:02:50 UTC  
> Url: https://github.com/boostorg/gil/pull/469#issuecomment-670020393  

Closing as it will be superseded by #500 .

---
