# #594 Add distance transform functionality [Open]

> Username: harshitpant1  
> Created at: 2021-04-03 05:01:02 UTC  
> Updated at: 2025-09-05 22:06:04 UTC  
> Url: https://github.com/boostorg/gil/pull/594  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Adds Distance Transform functionality to GIL, using algorithms which run in linear time in the worst case.  
  
### References  
  
Precise Euclidean distance transform -  
http://www.theoryofcomputing.org/articles/v008a019/v008a019.pdf  
  
Manhattan and chessboard distance transforms -  
Principles of Digital Image Processing - core Techniques by Wilhelm Burger, Mark J.Burge.  
  
Approximate Euclidean distance transform -  
http://www.cmm.mines-paristech.fr/~marcoteg/cv/publi_pdf/MM_refs/1986_Borgefors_distance.pdf  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: harshitpant1  
> Created_at: 2021-04-03 05:08:47 UTC  
> Updated_at: 2021-06-23 18:09:34 UTC  
> Url: https://github.com/boostorg/gil/pull/594#issuecomment-812812799  

Aside from the first wikipedia test, all other test have been verified with openCV.  
  
```  
import numpy as np  
import cv2 as cv  
  
################################################################################################  
# test_chessboard_uint16_t_input_uint8_t_output_distance_from_on_pixels  
  
img2 = np.array([[255,   0, 255, 255, 255, 255, 255],  
                 [255, 255, 255, 255, 255, 255, 255],  
                 [255, 255, 255, 255, 255, 255, 255],  
                 [255, 255, 255, 255, 255, 255, 255],  
                 [255, 255, 255,   0, 255, 255, 255],  
                 [255, 255, 255, 255, 255, 255, 255],  
                 [255, 255, 255, 255, 255, 255, 255]], dtype = np.uint8)  
  
#/ 8 bit (16 bit tested in gil) image used which is inverted to test distance from on pixels  
  
test2_chessboard_three = cv.distanceTransform(img2, cv.DIST_C,cv.DIST_MASK_3)  
print("\n test2_chessboard mask size 3: ")  
print(test2_chessboard_three)  
  
################################################################################################  
# test_euclidean_approx_and_manhattan_uint8_t_input_float32_t_output_distance_from_off_pixels  
  
img3 = np.array([  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255],  
 [255,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0, 255],  
 [255,   0, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,   0, 255],  
 [255,   0, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,   0, 255],  
 [255,   0, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,   0, 255],  
 [255,   0, 255, 255, 255,   0,   0,   0,   0,   0, 255, 255, 255,   0, 255],  
 [255,   0, 255, 255, 255,   0, 255, 255, 255,   0, 255, 255, 255,   0, 255],  
 [255,   0, 255, 255, 255,   0, 255, 255, 255,   0, 255, 255, 255,   0, 255],  
 [255,   0, 255, 255, 255,   0, 255, 255, 255,   0, 255, 255, 255,   0, 255],  
 [255,   0, 255, 255, 255,   0,   0,   0,   0,   0, 255, 255, 255,   0, 255],  
 [255,   0, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,   0, 255],  
 [255,   0, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,   0, 255],  
 [255,   0, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,   0, 255],  
 [255,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0,   0, 255],  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255]], dtype = np.uint8)  
  
test3_euclidean_five = cv.distanceTransform(img3, cv.DIST_L2,cv.DIST_MASK_5)  
print("\n test3_euclidean approx mask size 5: ")  
print(test3_euclidean_five)  
  
test3_manhattan_five = cv.distanceTransform(img3, cv.DIST_L1,cv.DIST_MASK_5)  
print("\n test3_manhattan approx mask size 5: ")  
print(test3_manhattan_five)  
  
################################################################################################  
# test_all_uint8_t_input_float32_t_ouptut_distance_from_off_pixels  
img4 = np.array([  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255],  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 0, 255, 255, 255, 255],  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255],  
 [255, 255, 0, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255],  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255],  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255],  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255],  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255],  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255],  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255],  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255],  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255],  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255],  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255],  
 [255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 0]], dtype = np.uint8)  
  
  
test4_precise_euclidean = cv.distanceTransform(img4, cv.DIST_L2,cv.DIST_MASK_PRECISE)  
print("\n test4_ Precise Euclidean: ")  
print(test4_precise_euclidean)  
  
test4_euclidean_three = cv.distanceTransform(img4, cv.DIST_L2,cv.DIST_MASK_3)  
print("\n test4_ Euclidean Approximation mask size 3 \n")  
print(test4_euclidean_three)  
  
test4_euclidean_five = cv.distanceTransform(img4, cv.DIST_L2,cv.DIST_MASK_5)  
print("\n test4_ Euclidean Approximation mask size 5: ")  
print(test4_euclidean_five)  
  
test4_manhattan_three = cv.distanceTransform(img4, cv.DIST_L1,cv.DIST_MASK_3)  
print("\n test4_ manhattan mask size 3: ")  
print(test4_manhattan_three)  
  
test4_manhattan_five = cv.distanceTransform(img4, cv.DIST_L1,cv.DIST_MASK_5)  
print("\n test4_ manhattan mask size 5: ")  
print(test4_manhattan_five)  
  
test4_chessboard_three = cv.distanceTransform(img4, cv.DIST_C,cv.DIST_MASK_3)  
print("\n test4_chessboard mask size 3: ")  
print(test4_chessboard_three)  
  
test4_chessboard_five = cv.distanceTransform(img4, cv.DIST_C,cv.DIST_MASK_5)  
print("\n test4_chessboard mask size 3: ")  
print(test4_chessboard_five)  
```

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-04-03 05:55:37 UTC  
> Updated_at: 2025-09-05 22:06:04 UTC  
> Url: https://github.com/boostorg/gil/pull/594#issuecomment-812817708  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/594?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 79.43%. Comparing base ([`bbdce36`](https://app.codecov.io/gh/boostorg/gil/commit/bbdce36a1f655ebf8332b775790673b197c8f1de?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`bd907af`](https://app.codecov.io/gh/boostorg/gil/commit/bd907aff9bb30f17b199d0d47c2837fe53f87cb7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 175 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #594      +/-   ##  
===========================================  
+ Coverage    78.72%   79.43%   +0.71%       
===========================================  
  Files          118      119       +1       
  Lines         5034     5208     +174       
===========================================  
+ Hits          3963     4137     +174       
  Misses        1071     1071                
```  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 3

> Username: harshitpant1  
> Created_at: 2021-06-23 18:05:19 UTC  
> Updated_at: 2021-08-26 11:25:17 UTC  
> Url: https://github.com/boostorg/gil/pull/594#issuecomment-867049010  

As you might have noticed at some places (especially in the test file) reformatting should have been done after using clang-format, but I had a feeling to keep things as they are after they have been automatically formatted.  
  
Let me know about your preferences on this.  
  
Note that the [second last commit](https://github.com/boostorg/gil/pull/594/files/76c6410c9cb421fc3dba02980624ea3e6b035f39) has proper manual formatting.

---

## Comment 4

> Username: mloskot  
> Created_at: 2021-06-25 08:08:38 UTC  
> Url: https://github.com/boostorg/gil/pull/594#issuecomment-868308436  

It is fine to try format any new code using the `.clang-format` that we are discussing as part of https://github.com/boostorg/gil/pull/596

---
