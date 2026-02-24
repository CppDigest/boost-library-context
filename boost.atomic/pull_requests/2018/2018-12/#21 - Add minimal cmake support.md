# #21 Add minimal cmake support  [Closed]

> Username: Mike-Devel  
> Created at: 2018-12-20 16:15:17 UTC  
> Updated at: 2019-01-02 22:22:05 UTC  
> Closed at: 2019-01-02 12:32:41 UTC  
> Url: https://github.com/boostorg/atomic/pull/21  

Second attempt with (hopefully) correct flags.   
(superseeds: https://github.com/boostorg/atomic/pull/20).

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-12-20 16:51:50 UTC  
> Url: https://github.com/boostorg/atomic/pull/21#issuecomment-449063627  

If you're interested in testing, this is how I do it in my guinea pig Assert:  
  
https://github.com/boostorg/assert/blob/develop/CMakeLists.txt#L23  
https://github.com/boostorg/assert/blob/develop/test/CMakeLists.txt  
  
`boost_test_jamfile` is not going to be able to figure out the current test/Jamfile, but if you remove the test-suite business and keep just the `run native_api.cpp ;` lines, it should work.

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2018-12-22 12:15:49 UTC  
> Url: https://github.com/boostorg/atomic/pull/21#issuecomment-449566066  

As per the discussion in #20 the cmake file no longer defines `BOOST_USE_WINDOWS_H` and  travis also test the build as a shared library.  
  
@pdimov: I'l have a look later. I don't feel quite comfortable with adding unit-test support in this PR, especially if it depends on external scripts, as that is imho out of scope of this "minimal" cmake support (remember, one of the design goals was easy maintenence for people that don't want to use cmake themselves). However, if @Lastique prefers to have the full package I can certainly add them too (either here or in a separate PR).  
  
I might not be able to again work on this during the next couple of days - so merry christmas everyone.

---

## Comment 3

> Username: Lastique  
> Created_at: 2019-01-02 12:32:41 UTC  
> Url: https://github.com/boostorg/atomic/pull/21#issuecomment-450852555  

Merged with corrections, thanks.

---

## Comment 4

> Username: Mike-Devel  
> Created_at: 2019-01-02 17:21:58 UTC  
> Url: https://github.com/boostorg/atomic/pull/21#issuecomment-450926652  

Thank you very much.

---
