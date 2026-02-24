# #118 Fix png grayalpha load [Merged]

> Username: adrianbroher  
> Created at: 2018-07-18 06:25:48 UTC  
> Updated at: 2019-08-07 20:34:17 UTC  
> Merged at: 2018-07-18 21:37:49 UTC  
> Closed at: 2018-07-18 21:37:49 UTC  
> Url: https://github.com/boostorg/gil/pull/118  

### Description  
  
This PR fixes #117 by querying for valid tRNS chunks within a PNG file and enabling libpng alpha palette to alpha channel conversion when the PNG file contains "simple transparency" (single value oder palette based alpha vallues).  
  
### Environment  
  
All relevant information like:  
  
- GIL version: boostorg/gil@c192814572d10605119ba47fcfcf25f62e1bdc42  
- Compiler version: Not applicable  
- Build settings: Not applicable  
  
### References  
  
#117  
  
### Tasklist  
  
- [x] Provide example image  
- [x] Implemented fix  
- [x] Add test case(s) (#125)  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
Regarding the test cases:  
I tried to run a `cmake` build, which failed during the linking stage of the `gil_test_ext_io_tiff` test execuable because of a undefined symbol `boost::system::generic_category()` so almost all of the io-based test executables are missing and I can't run `ctest`.  
  
Also could someone give me advise about writing a test a proper test for the linked issue? I used Boost Test a few times, but the existing io tests don't seem to assert anything about correctly loading an image and I can't think of any proper test assertions, maybe sampling some pixel for the expected values?

---

## Review 1 [Approved]

> Username: chhenning  
> Created_at: 2018-07-18 21:37:36 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/118#pullrequestreview-138449563  

Looks good to me!

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-07-23 11:59:47 UTC  
> Url: https://github.com/boostorg/gil/pull/118#issuecomment-407033320  

Hmm, shouldn't this wait until @adrianbroher questions about tests are addressed, helping him to complete the PR with tests?

---
