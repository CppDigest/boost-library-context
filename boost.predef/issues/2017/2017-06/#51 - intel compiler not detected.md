# #51 - intel compiler not detected [Open]

> Username: olk  
> Created at: 2017-06-04 12:17:10 UTC  
> Updated at: 2018-12-22 00:27:38 UTC  
> Url: https://github.com/boostorg/predef/issues/51  

Intel compiler 17 is not detected  
  
$ icc -v  
icc version 17.0.4 (gcc version 7.1.1 compatibility)  
  
unit-test/info_as_cpp (b2 toolset=intel):  
** Detected **  
BOOST_COMP_EDG = 41100000 (4,11,0) | EDG C++ Frontend  
** Not Detected **  
BOOST_COMP_INTEL = 0 | Intel C/C++

---

## Comment 1

> Username: grafikrobot  
> Created at: 2017-06-04 14:55:44 UTC  
> Url: https://github.com/boostorg/predef/issues/51#issuecomment-306044916  

At least on the [regression tests](url) it's detected as EDG and Intel emulated:  
  
```  
BOOST_COMP_EDG = 41100000 (4,11,0) | EDG C++ Frontend  
BOOST_COMP_GNUC_EMULATED = 40800005 (4,8,5) | Gnu GCC C/C++  
BOOST_COMP_INTEL_EMULATED = 170000002 (17,0,2) | Intel C/C++  
```  
Which is the best we can do until I resolve #28, but don't have the time to do that for the next release. I'll schedule that for the Fall release.

---

## Comment 2

> Username: olk  
> Created at: 2017-06-04 17:18:30 UTC  
> Url: https://github.com/boostorg/predef/issues/51#issuecomment-306053664  

BOOST_COMP_INTEL  || BOOST_COMP_INTEL_EMULATED helps, ty

---

## Comment 3

> Username: jeking3  
> Created at: 2017-11-10 22:55:59 UTC  
> Url: https://github.com/boostorg/predef/issues/51#issuecomment-343608716  

This is marked as 1.66.0, but it's being closed; does it need to move to 1.67.0?

---

## Comment 4

> Username: jeking3  
> Created at: 2018-01-23 13:32:32 UTC  
> Url: https://github.com/boostorg/predef/issues/51#issuecomment-359791244  

@grafikrobot This is marked as 1.66.0, but it's being closed; does it need to move to 1.67.0?

---

## Comment 5

> Username: jeking3  
> Created at: 2018-12-22 00:27:38 UTC  
> Url: https://github.com/boostorg/predef/issues/51#issuecomment-449529916  

Still happening in 19 Update 1.
