# #530 - Error in coding test #8 of TOMS748 [Closed]

> Username: jamadagni  
> Created at: 2021-02-11 16:28:42 UTC  
> Updated at: 2021-02-21 16:08:24 UTC  
> Closed at: 2021-02-21 16:08:24 UTC  
> Url: https://github.com/boostorg/math/issues/530  

https://github.com/boostorg/math/blob/a0a97b768afd4188c2985081fab937c0b655df4a/test/test_toms748_solve.cpp#L74  
  
This line should read ` return x * x - pow(1 - x, ip);` because in the original paper this test reads **x² − (1 − x)ⁿ** and n as a single integer input parameter from the paper is coded as `ip` here.  
  
I found this error by comparing the output of these tests to the values given in [the SciPy test suite](https://github.com/scipy/scipy/blob/master/scipy/optimize/_tstutils.py#L450-L454) and then checking what was wrong.

---

## Comment 1

> Username: sarthakdhingra21  
> Created at: 2021-02-13 08:55:46 UTC  
> Url: https://github.com/boostorg/math/issues/530#issuecomment-778586341  

@jamadagni Hey I want to work on this issue ,can u please guide me through how to test the output as I am new to this repo ?

---

## Comment 2

> Username: jamadagni  
> Created at: 2021-02-13 10:02:19 UTC  
> Url: https://github.com/boostorg/math/issues/530#issuecomment-778593427  

I have no idea how to compile this but there should be instructions. I have a separate implementation of this algorithm based on Boost, and I reported the bug by checking the source code against the original paper.
