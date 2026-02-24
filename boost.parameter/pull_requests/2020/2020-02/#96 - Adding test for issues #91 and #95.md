# #96 Adding test for issues #91 and #95 [Open]

> Username: urandon  
> Created at: 2020-02-10 10:40:33 UTC  
> Updated at: 2020-02-10 10:40:33 UTC  
> Url: https://github.com/boostorg/parameter/pull/96  

The test is related to issues #91 and #95 . It is a regression introduced to 1.71 release on compilers supporting C++11.  
  
Since 1.71 the result of `Params::foo_t` is `B` which means that provided parameter `type_foo_param` (set to `A`) is dropped and failed back to default one (`B`).

---
