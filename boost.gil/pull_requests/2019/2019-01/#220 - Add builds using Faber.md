# #220 Add builds using Faber [Closed]

> Username: mloskot  
> Created at: 2019-01-19 17:24:33 UTC  
> Updated at: 2020-03-20 20:43:57 UTC  
> Closed at: 2020-03-20 20:43:46 UTC  
> Url: https://github.com/boostorg/gil/pull/220  

Using current `develop` branch of https://github.com/stefanseefeld/faber  
  
### References  
  
* #188  
  
### Tasklist  
  
- [x] Report "Faber version unknown" https://github.com/stefanseefeld/faber/issues/13  
- [ ] Install Faber on Linux  
- [ ] Install Faber on Windows  
- [ ] Install Faber on MacOS  
- [ ] Configure basic GIL builds using Faber  
    - [ ] Update GIL `fabscript` files which have likely got out of sync  
    - [ ] Solve `faber cxxflags` propagation issue https://github.com/stefanseefeld/faber/issues/14  
- [ ] Review  
- [ ] Adjust for comments  
- [ ] **Restore temporarily removed CMake-based builds before merging!**  
- [ ] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-03-20 20:43:46 UTC  
> Url: https://github.com/boostorg/gil/pull/220#issuecomment-601902541  

Faber scripts have been rotting unmaintained for very long time hence removed in d2b560de744fde65317eb7c55e2460bcf8245cca

---
