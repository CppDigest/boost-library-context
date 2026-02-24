# #726 Fixed custom color converter in dynamic_factory and added corresponding [Merged]

> Username: nicolacandussi  
> Created at: 2023-02-07 22:59:16 UTC  
> Updated at: 2023-03-31 07:57:11 UTC  
> Merged at: 2023-02-21 13:38:03 UTC  
> Closed at: 2023-02-21 13:38:03 UTC  
> Url: https://github.com/boostorg/gil/pull/726  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
The function color_converted_view for dynamic views with a custom color converter ignores the provided color converter.   
This pr fixes this issue as well as some compilation failures related to it. A corresponding test is included

---

## Comment 1

> Username: mloskot  
> Created_at: 2023-02-08 09:09:42 UTC  
> Url: https://github.com/boostorg/gil/pull/726#issuecomment-1422265982  

Great, thanks!  
  
I will do my best to, over next days, investigate the CI failures with some older compilers, e.g.  
  
```  
====== BEGIN OUTPUT ======  
libs/gil/test/extension/dynamic_image/image_view_factory.cpp(148): test 'equal_pixels_values(result_view, gil::const_view(expected_image), 1e-3f)' failed in function 'static void test_color_converted_view::run()'  
1 error detected.  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```

---

## Comment 2

> Username: nicolacandussi  
> Created_at: 2023-02-12 13:58:55 UTC  
> Url: https://github.com/boostorg/gil/pull/726#issuecomment-1427040581  

I fixed the failing tests on Windows, that will probably fix the other CI failures for older compilers

---

## Comment 3

> Username: nicolacandussi  
> Created_at: 2023-02-21 13:31:50 UTC  
> Url: https://github.com/boostorg/gil/pull/726#issuecomment-1438497854  

Is there anything else I can do to get this PR merged? There are other similar issues in dynamic_image/image_view_factory.hpp that I would like to fix after this PR is closed.

---

## Review 4 [Approved]

> Username: mloskot  
> Created_at: 2023-02-21 13:37:39 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/726#pullrequestreview-1307441489  

Thank you for the reminder. This looks good, thanks!

---
