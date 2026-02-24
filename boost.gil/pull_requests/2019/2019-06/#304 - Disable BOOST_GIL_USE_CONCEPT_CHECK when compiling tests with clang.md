# #304 Disable BOOST_GIL_USE_CONCEPT_CHECK when compiling tests with clang [Merged]

> Username: mloskot  
> Created at: 2019-06-01 21:12:18 UTC  
> Updated at: 2019-06-02 04:13:11 UTC  
> Merged at: 2019-06-02 04:13:05 UTC  
> Closed at: 2019-06-02 04:13:05 UTC  
> Url: https://github.com/boostorg/gil/pull/304  

Avoid Clang's flooding of non-disableable warnings like:  
  
> T does not declare any constructor to initialize its non-modifiable members  
  
when compiling with concepts check enabled.  
  
Bug 41759: `warn_no_constructor_for_refconst` can not be disabled (Boost.ConceptCheck)  
https://bugs.llvm.org/show_bug.cgi?id=41759  
  
### Environment  
  
All relevant information like:  
  
- Compiler version: clang  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---
