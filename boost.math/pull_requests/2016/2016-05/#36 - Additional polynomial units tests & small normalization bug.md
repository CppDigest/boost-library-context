# #36 Additional polynomial units tests & small normalization bug [Merged]

> Username: kundor  
> Created at: 2016-05-02 18:13:54 UTC  
> Updated at: 2016-05-02 18:30:05 UTC  
> Merged at: 2016-05-02 18:30:05 UTC  
> Closed at: 2016-05-02 18:30:05 UTC  
> Url: https://github.com/boostorg/math/pull/36  

I added a unit test for the polynomial self-multiply-assignment problem fixed in pull request #35.  
  
Also, I added a missing call to `normalize()` in `operator=(std::initializer_list<T> l)`, which allowed polynomials to exist with zero leading coefficients, and added tests to check that polynomials constructed and assigned with initalizer lists are normalized (which fail before commit 4302398, and succeed afterward.)

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2016-05-02 18:30:01 UTC  
> Url: https://github.com/boostorg/math/pull/36#issuecomment-216320191  

Thanks!

---
