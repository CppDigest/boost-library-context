# #119 Optimize s390x instructions [Merged]

> Username: nealef  
> Created at: 2019-09-20 17:32:51 UTC  
> Updated at: 2019-09-21 06:57:21 UTC  
> Merged at: 2019-09-21 06:57:21 UTC  
> Closed at: 2019-09-21 06:57:21 UTC  
> Url: https://github.com/boostorg/context/pull/119  

Some optimizations for the s390x implementation of jump/make/ontop:  
- Use multiple register instructions rather than individual load/stores  
- Use floating point instructions to load or store from/to storage  
- Use symbols to map CTX rather than explicit displacements  
- Branch to _exit@PLT to avoid relocation complaints  
- Use immediate instructions for adding to registers  
  
Builds and passes fcontext tests using gcc-4.8.5 and devtoolset-8-gcc-c++ (8.3.1).  
  
Do you need access to a jenkins worker so you can build/test s390x automatically?

---

## Comment 1

> Username: olk  
> Created_at: 2019-09-21 06:57:16 UTC  
> Url: https://github.com/boostorg/context/pull/119#issuecomment-533774281  

very nice - ty  
boost uses Travis CI - unfortunately the code is not build and executed on a s390x system

---
