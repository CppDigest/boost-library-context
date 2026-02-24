# #383 Add implementatin of box filter and blur [Merged]

> Username: miralshah365  
> Created at: 2019-08-15 23:50:00 UTC  
> Updated at: 2020-02-22 13:07:55 UTC  
> Merged at: 2019-08-21 18:56:37 UTC  
> Closed at: 2019-08-21 18:56:37 UTC  
> Url: https://github.com/boostorg/gil/pull/383  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
implemented `blur` function  
  
tests added for `box_filter`  
  
This utility will provide a way to remove noise from the image using convolution with `average` kernel(normalized and unnormalized).  
  
It also provides `blur` function which works only with normalized mean kernels  
  
<!-- What does this pull request do? -->  
  
### References  
closes #382  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Commented]

> Username: mloskot  
> Created_at: 2019-08-18 15:05:46 UTC  
> State: DISMISSED  
> Url: https://github.com/boostorg/gil/pull/383#pullrequestreview-276283005  

@miralshah365 LGTM. Feel free to merge.

---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2019-08-18 16:47:44 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/383#pullrequestreview-276286905  

`run box_filter.cpp ;` entry is missing from here:  
  
https://github.com/boostorg/gil/blob/ec30419a3441ea59bc3ce8a742798a305c02957f/test/core/image_processing/Jamfile#L17  
  
Currently, the `box_filter.cpp` test is not compiled on Jamfile-only CI jobs on Travis CI and AppVeyor.

---

## Review 3 [Commented]

> Username: mloskot  
> Created_at: 2019-08-18 17:04:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/383#pullrequestreview-276287472  

📁 include/boost/gil/image_processing/filter.hpp

```diff
  27 |+     long int anchor = -1,
  28 |+     bool normalize=true,
  29 |+     convolve_boundary_option option = convolve_option_extend_zero
```

> Username: mloskot  
> Created_at: 2019-08-18 17:04:28 UTC  
> Updated_at: 2019-08-21 17:17:19 UTC  
> Url: https://github.com/boostorg/gil/pull/383#discussion_r314994067  

@miralshah365 The `option` seems unused.


---

## Review 4 [Commented]

> Username: mloskot  
> Created_at: 2019-08-18 17:05:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/383#pullrequestreview-276287503  

📁 include/boost/gil/image_processing/filter.hpp

```diff
  42 |+     else { kernel_values.resize(kernel_size, 1.0f); }
  43 |+ 
  44 |+     if (anchor == -1) anchor = kernel_size / 2;
```

> Username: mloskot  
> Created_at: 2019-08-18 17:05:02 UTC  
> Updated_at: 2019-08-21 17:17:19 UTC  
> Url: https://github.com/boostorg/gil/pull/383#discussion_r314994083  

@miralshah365 Assignment of unsigned to signed needs `static_cast<int>` to avoid warnings here.


---

## Review 5 [Commented]

> Username: mloskot  
> Created_at: 2019-08-18 17:05:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/383#pullrequestreview-276287540  

📁 test/core/image_processing/box_filter.cpp

```diff
  13 |+ #include <boost/gil/algorithm.hpp>
  14 |+ #include <boost/gil/gray.hpp>
  15 |+ #include <boost/core/lightweight_test.hpp>
```

> Username: mloskot  
> Created_at: 2019-08-18 17:05:57 UTC  
> Updated_at: 2019-08-21 17:17:19 UTC  
> Url: https://github.com/boostorg/gil/pull/383#discussion_r314994120  

@miralshah365 Since Boost.Test is used, `#include <boost/core/lightweight_test.hpp>` must be removed, otherwise `BOOST_TEST` and other macros will clash.


---

## Review 6 [Commented]

> Username: mloskot  
> Created_at: 2019-08-18 17:15:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/383#pullrequestreview-276287858  

📁 include/boost/gil/image_processing/filter.hpp

```diff
  53 |+     DstView const& dst_view,
  54 |+     std::size_t kernel_size,
  55 |+     std::size_t anchor = kernel_size / 2,
```

> Username: mloskot  
> Created_at: 2019-08-18 17:15:34 UTC  
> Updated_at: 2019-08-21 17:17:19 UTC  
> Url: https://github.com/boostorg/gil/pull/383#discussion_r314994422  

@miralshah365 Such re-use of arguments is not valid C++ as per http://eel.is/c++draft/dcl.fct.default#9  
  
> A default argument is evaluated each time the function is called with no argument for the corresponding parameter.  
> A parameter shall not appear as a potentially-evaluated expression in a default argument.  
> Parameters of a function declared before a default argument are in scope and can hide namespace and class member names.  
>   
> ```  
> int a;  
> int f(int a, int b = a);            // error: parameter a used as default argument  
> ```


---

## Review 7 [Changes requested]

> Username: mloskot  
> Created_at: 2019-08-18 19:28:40 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/383#pullrequestreview-276292491  

@miralshah365 There is one request for change to fix the test linking error

📁 test/core/image_processing/Jamfile

```diff
  20 | run lanczos_scaling.cpp ;
  21 | run simple_kernels.cpp ;
  22 |+ run box_filter.cpp ;
```

> Username: mloskot  
> Created_at: 2019-08-18 19:28:11 UTC  
> Updated_at: 2019-08-21 17:17:19 UTC  
> Url: https://github.com/boostorg/gil/pull/383#discussion_r314999031  

@miralshah365 Sorry for incomplete previous suggestion.  
  
This should read something like this  
  
```  
run box_filter.cpp /boost/test//boost_unit_test_framework : : : <link>shared:<define>BOOST_TEST_DYN_LINK=1 ;  
```


---

## Review 8 [Approved]

> Username: mloskot  
> Created_at: 2019-08-21 08:15:56 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/383#pullrequestreview-277625763  

@miralshah365 looks good, feel free to merge.  
  
Looks like you'll need to resolve conflict

---
