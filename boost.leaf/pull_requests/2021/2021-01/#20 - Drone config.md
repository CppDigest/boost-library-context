# #20 Drone config [Merged]

> Username: sdarwin  
> Created at: 2021-01-28 14:50:07 UTC  
> Updated at: 2021-04-25 23:39:57 UTC  
> Merged at: 2021-04-25 23:39:57 UTC  
> Closed at: 2021-04-25 23:39:57 UTC  
> Url: https://github.com/boostorg/leaf/pull/20  

Drone is a continuous integration framework similar to Travis CI. [The C++ Alliance](https://cppalliance.org/) is offering a hosted Drone server for Boost libraries. Please refer to https://github.com/CPPAlliance/drone-ci for more information and instructions.

---

## Comment 1

> Username: zajo  
> Created_at: 2021-01-28 18:20:00 UTC  
> Url: https://github.com/boostorg/leaf/pull/20#issuecomment-769280836  

Thanks. Can you please remove all asciidoctor and DOC=1 environment stuff? This builds and publishes the documentation, which is done on GitHub Actions already and should not be done on Drone too.

---
