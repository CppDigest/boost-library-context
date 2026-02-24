# #666 chore: Remove unused variable in diffusion.hpp [Merged]

> Username: striezel  
> Created at: 2022-05-11 16:48:14 UTC  
> Updated at: 2022-05-12 17:34:00 UTC  
> Merged at: 2022-05-12 12:23:49 UTC  
> Closed at: 2022-05-12 12:23:49 UTC  
> Url: https://github.com/boostorg/gil/pull/666  

### Description  
  
The variable `dims` is not used and can cause a compiler warning.  
  
### References  
  
It causes compiler warning, e.g. here:  
https://web.archive.org/web/20220511164304/https://www.boost.org/development/tests/develop/output/teeks99-06-dc9-2a-64onaarch64-gil-clang-linux-9~c++2a-warnings.html#anisotropic_diffusion (archived from [the original](https://www.boost.org/development/tests/develop/output/teeks99-06-dc9-2a-64onaarch64-gil-clang-linux-9~c++2a-warnings.html#anisotropic_diffusion), because that changes with every build on the `develop` branch)  
  
```  
../boost/gil/image_processing/diffusion.hpp:373:19: warning: unused variable 'dims' [-Wunused-variable]  
1 warning generated.  
```  
  
### Tasklist  
  
- [ ] Ensure all CI builds pass  
- [x] Review and approve

---
