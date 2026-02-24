# #59 Fixed bug where pointer iserializer can be NULL [Merged]

> Username: vtnerd  
> Created at: 2017-08-05 03:22:16 UTC  
> Updated at: 2017-08-11 13:13:13 UTC  
> Merged at: 2017-08-11 13:13:13 UTC  
> Closed at: 2017-08-11 13:13:13 UTC  
> Url: https://github.com/boostorg/serialization/pull/59  

The test case causes a segfault, and is similar to [this bug report](https://svn.boost.org/trac10/ticket/9370). I think this fix should be added because the check is simple and its possible to craft a file that will cause undefined behavior / null pointer dereference. The only work-around for users of the library is to register every type as a pointer type (even those that are not used as pointers) which is a bit unexpected.  
  
Also, sorry for being 11th hour before boost 1.65 tagged release ...

---
