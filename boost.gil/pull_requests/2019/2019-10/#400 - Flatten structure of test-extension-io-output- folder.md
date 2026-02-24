# #400 Flatten structure of test/extension/io/output/ folder [Merged]

> Username: mloskot  
> Created at: 2019-10-28 22:54:08 UTC  
> Updated at: 2019-10-29 08:00:04 UTC  
> Merged at: 2019-10-29 07:59:55 UTC  
> Closed at: 2019-10-29 07:59:56 UTC  
> Url: https://github.com/boostorg/gil/pull/400  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
There is no need for format-specific subfolders.  
There is no code in place that checks if all those folders exist and creates them.  
This change simplifies the structure to simplify running of I/O tests.  
  
Rename `images/jpg/` to `images/jpeg/` in tests.  
The `jpeg` is name of the format used in the I/O extension and the folders inside `images/` are named after those formats.  
  
----  
  
This in fact fixes tests run for build with `BOOST_GIL_IO_TEST_ALLOW_READING_IMAGES` and `BOOST_GIL_IO_TEST_ALLOW_WRITING_IMAGES` macros defined in case the `output/*` structure is missing.  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass (see https://github.com/boostorg/gil/pull/400#issuecomment-547299907)

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-10-29 07:58:47 UTC  
> Url: https://github.com/boostorg/gil/pull/400#issuecomment-547299907  

Travis for boostorg/gil is still pending - https://travis-ci.org/boostorg/gil/builds/604176547  
Travis for mloskot/gil succeeded -https://travis-ci.org/mloskot/gil/builds/604175350  
I take the latter.

---
