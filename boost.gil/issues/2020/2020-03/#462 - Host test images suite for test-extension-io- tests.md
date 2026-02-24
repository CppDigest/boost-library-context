# #462 - Host test images suite for test/extension/io/ tests [Open]

> Username: mloskot  
> Created at: 2020-03-22 00:09:48 UTC  
> Updated at: 2020-03-22 00:16:42 UTC  
> Url: https://github.com/boostorg/gil/issues/462  

There is a need for structured and complete test images suite used available for contributors to run the `test/extension/io/` tests. All details can be found in the mailing list thread (link below). Currently, by default, the test actually test little or nothing unless built with defines. We need to get rid of tweaking tests content with compile-time macros:  
- `BOOST_GIL_IO_TEST_ALLOW_READING_IMAGES`  
- `BOOST_GIL_IO_TEST_ALLOW_READING_IMAGES`  
- `BOOST_GIL_IO_USE_PNG_TEST_SUITE_IMAGES`  
  
and ensure that if tests run, they do always exercise the test I/O, unconditionally.  
  
First thing to achieve that is to maintain collection of test images for I/O tests, similarly to, for example, [Julia test images](https://github.com/JuliaImages/TestImages.jl).  
The images could be hosted in dedicated repo https://github.com/boost-gil/test-images, bundled in Zip archives for efficient download by a dedicated script.  
  
### References  
  
- [[IO] Hosting test images](https://lists.boost.org/boost-gil//2019/07/0279.php) discussion on boost-gil list  
- Requires task #461  
  
### Tasklist  
  
- [ ] Collect all images  
- [ ] Script downloading archives and deploying images where expected.  
- [ ] Configure CI builds running the full I/O tests.
