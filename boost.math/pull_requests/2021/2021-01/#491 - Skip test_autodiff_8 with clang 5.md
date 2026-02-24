# #491 Skip test_autodiff_8 with clang 5 [Merged]

> Username: mborland  
> Created at: 2021-01-14 17:16:47 UTC  
> Updated at: 2021-01-15 16:42:13 UTC  
> Merged at: 2021-01-15 16:35:31 UTC  
> Closed at: 2021-01-15 16:35:31 UTC  
> Url: https://github.com/boostorg/math/pull/491  

CI tests using clang 5 fail at test_autodiff_8 for all language standards. Not an issue with clang 6 and on.

---

## Review 1 [Approved]

> Username: pulver  
> Created_at: 2021-01-14 17:34:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/491#pullrequestreview-568467231  

Looks good, thank you!

---

## Review 2 [Changes requested]

> Username: pulver  
> Created_at: 2021-01-14 20:11:33 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/math/pull/491#pullrequestreview-568588834  

📁 test/test_autodiff_8.cpp

```diff
   9 | BOOST_AUTO_TEST_SUITE(test_autodiff_8)
  10 | 
  11 |+ #if __clang_major__ > 5 || __GNUC__ > 5 || defined(_MSC_VER)
```

> Username: pulver  
> Created_at: 2021-01-14 20:11:01 UTC  
> Updated_at: 2021-01-15 13:08:18 UTC  
> Url: https://github.com/boostorg/math/pull/491#discussion_r557669609  

Should the `#if` block surround the entire test suite? Otherwise as you may have seen:  
```  
testing.capture-output ../../../bin.v2/libs/math/test/test_autodiff_8.test/clang-linux-5.0.0/release/link-static/threading-multi/visibility-hidden/test_autodiff_8.run  
====== BEGIN OUTPUT ======  
Test setup error: test tree is empty  
  
EXIT STATUS: 200  
====== END OUTPUT ======  
```

> Username: mborland  
> Created_at: 2021-01-15 13:04:51 UTC  
> Updated_at: 2021-01-15 13:08:18 UTC  
> Url: https://github.com/boostorg/math/pull/491#discussion_r558293130  

Surrounding the entire test suite yields the same error. I moved the `#if` block to right above the test that fails.


---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-01-14 21:25:22 UTC  
> Url: https://github.com/boostorg/math/pull/491#issuecomment-760486210  

@mborland : Do you mind rebasing on develop? I just fixed a pile of issues as well are merged your other fix, so there's a chance of this going green.

---

## Comment 4

> Username: mborland  
> Created_at: 2021-01-15 13:16:54 UTC  
> Url: https://github.com/boostorg/math/pull/491#issuecomment-760936225  

@NAThompson Rebased. Makes the commit history look strange but there are no merge conflicts.

---

## Comment 5

> Username: mborland  
> Created_at: 2021-01-15 16:07:55 UTC  
> Url: https://github.com/boostorg/math/pull/491#issuecomment-761031620  

@NAThompson Not green, but down to single digit errors. Linux is clean.

---
