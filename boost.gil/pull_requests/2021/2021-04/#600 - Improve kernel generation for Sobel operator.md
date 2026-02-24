# #600 Improve kernel generation for Sobel operator [Open]

> Username: meshtag  
> Created at: 2021-04-29 15:24:03 UTC  
> Updated at: 2021-05-12 07:35:05 UTC  
> Url: https://github.com/boostorg/gil/pull/600  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
This pull request intends to extend support for Sobel kernel generation in Gil. It is an improvisation over #562 in terms of performance as well as feature completeness and follows a completely different approach. This API is constructed on top of already existing implementation of 2D convolution as suggested [here](https://github.com/boostorg/gil/pull/562#issuecomment-789028239) by @simmplecoder and will hence benefit directly when improvements in 2D convolution are made during GSoC 2021.  
  
<!-- What does this pull request do? -->  
Created API returns a Sobel kernel corresponding to user specified horizontal and vertical derivative for images. It also provides an option to specify the size of desired kernel. Generated kernels were verified with Opencv kernels. The script used for obtaining required Opencv kernels is :   
```  
import numpy as np  
import cv2   
  
# sobel_x_order_y_order_size = cv2.getDerivKernels(y_order, x_order, size)  
  
sobel_6_0_15 = cv2.getDerivKernels(0, 6, 15)  
sobel_7_0_15 = cv2.getDerivKernels(0, 7, 15)  
sobel_9_0_19 = cv2.getDerivKernels(0, 9, 19)  
  
sobel_0_2_15 = cv2.getDerivKernels(2, 0, 15)  
sobel_0_3_15 = cv2.getDerivKernels(3, 0, 15)  
sobel_0_4_15 = cv2.getDerivKernels(4, 0, 15)  
sobel_0_5_15 = cv2.getDerivKernels(5, 0, 15)  
  
sobel_3_3_19 = cv2.getDerivKernels(3, 3, 19)  
sobel_4_4_19 = cv2.getDerivKernels(4, 4, 19)  
sobel_1_7_19 = cv2.getDerivKernels(7, 1, 19)  
sobel_5_1_19 = cv2.getDerivKernels(1, 5, 19)  
  
print (np.outer(sobel_6_0_15[0], sobel_6_0_15[1]))  
print (np.outer(sobel_7_0_15[0], sobel_7_0_15[1]))  
print (np.outer(sobel_9_0_19[0], sobel_9_0_19[1]))  
  
print (np.outer(sobel_0_2_15[0], sobel_0_2_15[1]))  
print (np.outer(sobel_0_3_15[0], sobel_0_3_15[1]))  
print (np.outer(sobel_0_4_15[0], sobel_0_4_15[1]))  
print (np.outer(sobel_0_5_15[0], sobel_0_5_15[1]))  
  
print (np.outer(sobel_3_3_19[0], sobel_3_3_19[1]))  
print (np.outer(sobel_4_4_19[0], sobel_4_4_19[1]))  
print (np.outer(sobel_1_7_19[0], sobel_1_7_19[1]))  
print (np.outer(sobel_5_1_19[0], sobel_5_1_19[1]))  
```  
  
The idea of repeated kernel convolution is well known but its implementation present here depends largely on my own intuition, hence I have tried to test it with as many random test cases as I could. This inevitably lead to a somewhat large test file. However, if the size of test file is unacceptably large, I can remove some tests and keep only the most complicated ones.  
  
I have used Gil's ```gray32f_view_t``` for storing kernel data with the motive of avoiding dynamic memory allocation and using inbuilt ```convolve2d()``` function. One possible disadvantage that stems from this fact is that we cannot obtain kernels containing very large numbers since we do not have more than 32 bits to store them. One possible workaround is to use a ```std::array``` for storing  kernel data/numbers as ```float/double``` and overload ```convolve2d()``` for handling ```std::array```(perhaps this could be added in GSoC bucketlist for project 1?).   
The implementation is completely designed by me and possibly differs from those in other standard libraries such as OpenCv, Scikit, etc(I have not investigated them yet).    
  
A schematic representation of the used workflow is attached [here](https://github.com/meshtag/gil/blob/backup1/Screenshot%20from%202021-04-21%2010-39-07.png) for the convenience of reviewer(s).  
This is an experimental feature and I do not expect a quick merge (we can chose to bring it in after modifying ```convolve2d()``` as mentioned above).  
  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
https://stackoverflow.com/a/10032882/14958679  
https://github.com/boostorg/gil/pull/562#issuecomment-789018088  
Depends on #577  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-04-29 16:27:59 UTC  
> Updated_at: 2021-04-29 17:05:45 UTC  
> Url: https://github.com/boostorg/gil/pull/600#issuecomment-829388144  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/600?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#600](https://codecov.io/gh/boostorg/gil/pull/600?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e51722f) into [develop](https://codecov.io/gh/boostorg/gil/commit/bbdce36a1f655ebf8332b775790673b197c8f1de?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (bbdce36) will **increase** coverage by `0.69%`.  
> The diff coverage is `96.25%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #600      +/-   ##  
===========================================  
+ Coverage    78.72%   79.41%   +0.69%       
===========================================  
  Files          118      118                
  Lines         5034     5199     +165       
===========================================  
+ Hits          3963     4129     +166       
+ Misses        1071     1070       -1       
```

---

## Comment 2

> Username: harshitpant1  
> Created_at: 2021-05-11 12:00:44 UTC  
> Updated_at: 2021-05-11 12:03:17 UTC  
> Url: https://github.com/boostorg/gil/pull/600#issuecomment-838349644  

Hi @meshtag,  
I'm writing this message regarding the issue with numeric limits of `gray32f_image_t`.  
And this is not to say specializing `convolve_2d `for other containers is a bad Idea.  
  
But I also wonder why only `gray64f_pixel_t `and `gray64f_image_t `are defined (and those too [only in the IO directory](https://github.com/boostorg/gil/blob/develop/include/boost/gil/io/typedefs.hpp)).  
Had GIL contained `gray64f_view_t `as well, this issue wouldn't arise at all.   
As `gray64f_pixel_t `uses double (64 bits) as the base type, it [can store larger numbers](https://en.cppreference.com/w/cpp/language/types) and it works correctly as can be seen [here](https://wandbox.org/permlink/bZPmme5E6dgY9dlA).  
  
However, I might be missing something, if that's the case, please correct me.

---

## Comment 3

> Username: meshtag  
> Created_at: 2021-05-11 17:24:15 UTC  
> Updated_at: 2021-05-12 07:35:05 UTC  
> Url: https://github.com/boostorg/gil/pull/600#issuecomment-838867198  

Hi @harsh-4 ,   
Thanks for the feedback,  
yes your argument regarding missing ```gray64f_view_t``` in ```gil``` seems sound to me and I can't think of any solid reason about its non existence at the moment. We can create a workaround for numeric limits problem by either modifying ```convolve_2d``` for ```std::vector``` / other similar containers or we can introduce ```gray64f_view_t```. My inclination towards the former was simply because this application(kernel generation) seems like a pretty specialised case for numeric limits overflow/underflow, I am not sure whether we require ```gray64f_view_t``` for reading a significant number of real world images. However, I might be wrong.

---

## Comment 4

> Username: harshitpant1  
> Created_at: 2021-05-11 19:48:32 UTC  
> Url: https://github.com/boostorg/gil/pull/600#issuecomment-839072677  

Thanks for taking out the time, @meshtag  
  
> I am not sure whether we require gray64f_view_t for reading a significant number of real world images.  
  
Yes that is a sound argument and might be the reason why `gray64f_view_t` was never created and other `64f` typedefs weren't put [here](https://github.com/boostorg/gil/blob/develop/include/boost/gil/typedefs.hpp).  
  
Thus, modification or an entirely different specialization (as interface of std containers like `std::vector` differs a lot from GIL views) of `convolve_2d` also seems to be a good idea to me.  
  
And honestly, I had no idea when I saw your message (on slack) that `gray64f_view_t` didn't exist. But when I found that out later, I still wrote the message thinking a missing `gray64f_view_t` (when `gray64f_pixel_t` and `gray64f_image_t` existed) might still be worth mentioning.  
  
Other than that, thanks for explaining your thought process on this to me.

---

## Comment 5

> Username: meshtag  
> Created_at: 2021-05-12 07:07:44 UTC  
> Url: https://github.com/boostorg/gil/pull/600#issuecomment-839521276  

Happy to explain! Thanks for your input :)

---
