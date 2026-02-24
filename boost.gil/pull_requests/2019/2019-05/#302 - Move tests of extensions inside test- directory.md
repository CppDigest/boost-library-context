# #302 Move tests of extensions inside test/ directory [Merged]

> Username: mloskot  
> Created at: 2019-05-27 20:23:18 UTC  
> Updated at: 2019-05-28 18:03:56 UTC  
> Merged at: 2019-05-28 16:58:22 UTC  
> Closed at: 2019-05-28 16:58:22 UTC  
> Url: https://github.com/boostorg/gil/pull/302  

Split header tests accordingly i.e. test core headers as part of  
core tests, numeric extension headers as part of numeric tests, etc.  
  
It extends the convention of sub-directories already established in  
`include/boost/gil` directory. It is sensible to follow it in other  
areas of the source tree (i.e. `test/`, `doc/` and `benchmark/`).  
  
Another important reason to move the tests is to enable removal of  
the top-level `Jamfile` with all its definitions of test-specific  
requirements.  
The top-level `Jamfile` is not advised, especially if it specifies  
build requirements like C++ language version.  
Those affect non-tests builds e.g. documentation, causing failures  
during generation of HTML documentation (leads to missing docs).  
  
### References  
  
* Changes in #260 broke build of the docs during global docs build.  
  
### Tasklist  
  
- [x] Update Boost.Build configuration in `Jamfile`-s  
- [x] Update CMake configuration `CMakeLists.txt`-s  
- [x] Ensure all CI builds pass  
- [x] Review and approve  
  
  
-----  
  
FYI, The unwanted top-level `Jamfile` has been confirmed (by @djarek and @pdimov) as what caused GIL docs build failures and not publishing them as part of Boost 1.70 docs.

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-05-28 18:03:55 UTC  
> Url: https://github.com/boostorg/gil/pull/302#issuecomment-496625644  

@stefanseefeld We may have to merge into `master` sooner to get this change (and fix the docs build).  
  
This looks good now  
https://www.boost.org/doc/libs/develop/libs/gil/doc/html/index.html  
whereas this does not  
https://www.boost.org/doc/libs/master/libs/gil/doc/html/index.html

---
