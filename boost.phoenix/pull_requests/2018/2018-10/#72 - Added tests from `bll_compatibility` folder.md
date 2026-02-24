# #72 Added tests from `bll_compatibility` folder [Merged]

> Username: Kojoley  
> Created at: 2018-10-25 01:47:14 UTC  
> Updated at: 2019-03-14 11:21:45 UTC  
> Merged at: 2019-03-14 11:05:11 UTC  
> Closed at: 2019-03-14 11:05:11 UTC  
> Url: https://github.com/boostorg/phoenix/pull/72  

Closes #71

---

## Comment 1

> Username: Flast  
> Created_at: 2019-01-16 23:29:02 UTC  
> Url: https://github.com/boostorg/phoenix/pull/72#issuecomment-454983782  

I think that is GCC's defect, but I'm trying to break down into minimal repro.  
```  
testing.capture-output bin.v2/libs/phoenix/test/result_of_tests.test/gcc-4.8/debug/cxxstd-11-iso/visibility-hidden/result_of_tests.run  
====== BEGIN OUTPUT ======  
libs/phoenix/test/bll_compatibility/result_of_tests.cpp(304): test 'apply1<int>(_1, one) == 1' ('32765' == '1') failed in function 'int main()'  
1 error detected.  
EXIT STATUS: 1  
====== END OUTPUT ======  
```

---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-01-16 23:41:12 UTC  
> Url: https://github.com/boostorg/phoenix/pull/72#issuecomment-454986430  

I am not sure those tests are needed, they test Lambda (only few were converted to test Phoenix, I spotted it too late).

---

## Comment 3

> Username: Flast  
> Created_at: 2019-01-16 23:59:17 UTC  
> Url: https://github.com/boostorg/phoenix/pull/72#issuecomment-454990213  

Indeed. We can comment out the tests.

---

## Comment 4

> Username: Flast  
> Created_at: 2019-01-17 00:20:16 UTC  
> Url: https://github.com/boostorg/phoenix/pull/72#issuecomment-454994394  

Ah, OK I understood. A simple lambda, such as just a placeholder, will pass through its arguments **as reference** and dangling reference in `apply1` (return a reference to argument). So, can you comment out the line, @Kojoley ?

---

## Comment 5

> Username: Kojoley  
> Created_at: 2019-01-17 00:33:32 UTC  
> Url: https://github.com/boostorg/phoenix/pull/72#issuecomment-454997028  

> Ah, OK I understood. A simple lambda, such as just a placeholder, will pass through its arguments as reference and dangling reference in `apply1` (return a reference to argument).  
  
`A` is `int&`, the argument is a variable `one`, if it returns a reference it should be a reference to `one`.  
  
> So, can you comment out the line, @Kojoley ?  
  
Again, the whole file tests Lambda, not Phoenix, what is the point? I can just leave only `algorithm_test` and `bind_tests_advanced` as they were converted to test Phoenix.

---

## Comment 6

> Username: Flast  
> Created_at: 2019-01-17 13:15:24 UTC  
> Url: https://github.com/boostorg/phoenix/pull/72#issuecomment-455167990  

> > Ah, OK I understood. A simple lambda, such as just a placeholder, will pass through its arguments as reference and dangling reference in `apply1` (return a reference to argument).  
>   
> `A` is `int&`, the argument is a variable `one`, if it returns a reference it should be a reference to `one`.  
  
No, `A` is `int` because of  `apply1 **<int>** (_1, one)`.  
  
> > So, can you comment out the line, @Kojoley ?  
>   
> Again, the whole file tests Lambda, not Phoenix, what is the point? I can just leave only `algorithm_test` and `bind_tests_advanced` as they were converted to test Phoenix.  
  
Fair enough.

---

## Comment 7

> Username: Flast  
> Created_at: 2019-03-14 11:05:14 UTC  
> Url: https://github.com/boostorg/phoenix/pull/72#issuecomment-472804932  

I'm so sorry for leaving this PR (I kind of remember I waited CI completion...).

---
