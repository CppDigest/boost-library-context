# #37 Disable variadics with some older compiler. [Merged]

> Username: Flast  
> Created at: 2016-08-17 13:43:11 UTC  
> Updated at: 2016-08-17 13:43:20 UTC  
> Merged at: 2016-08-17 13:43:18 UTC  
> Closed at: 2016-08-17 13:43:18 UTC  
> Url: https://github.com/boostorg/phoenix/pull/37  

which cannot expand parameter pack into fixed length templates.  
  
see [teeks99-02-dg4.4-g0x-Docker-64on64 - phoenix - actor / gcc-4.4~gnu0x](http://www.boost.org/development/tests/develop/developer/output/teeks99-02-dg4-4-g0x-Docker-64on64-boost-bin-v2-libs-phoenix-test-actor-test-gcc-4-4~gnu0x-debug.html)  
  
Tested on GCC 4.4 (Fedora 12), GCC 6.1.1 (Fedora rawhide) and Clang 3.8.0 (Fedora rawhide).

---
