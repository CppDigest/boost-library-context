# #356 - [gsoc2019] 2D convolution and kernels [Closed]

> Username: miralshah365  
> Created at: 2019-07-27 19:19:43 UTC  
> Updated at: 2020-02-22 13:10:14 UTC  
> Closed at: 2019-08-08 21:47:10 UTC  
> Url: https://github.com/boostorg/gil/issues/356  

Add new feature in numeric extension for 2D convolution and 2d kernel. See https://github.com/boostorg/gil/issues/356#issuecomment-515893526 for more details about motivation.  
  
To achieve the goal new APIs for 2D can be designed in the following corresponding to their 1D counterparts:  
1. kernle_1d --> kernel_2d  
2. kernel_1d_fixed --> kernel_2d_fixed  
3. convolve_row/convolve_col --> convolve_2d

---

## Comment 1

> Username: mloskot  
> Created at: 2019-07-28 21:59:32 UTC  
> Url: https://github.com/boostorg/gil/issues/356#issuecomment-515798623  

@miralshah365  
This overview should clarify some obvious questions:  
  
- What is the motivation?  
- What problems is this plan going to solve?  
- What prevents kernel_1d from being a used in 2D context?   
- What is your evaluation of `convolve`  recently added by @lpranam w.r.t. your plan?  
- What is lacking in the numeric's [2D separable convolution](https://github.com/boostorg/gil/blob/develop/include/boost/gil/extension/numeric/convolve.hpp#L29)?   
  
Here are some archived posts related to this topic:  
- https://sourceforge.net/p/adobe-source/discussion/648138/thread/175ce462/  
- https://lists.boost.org/Archives/boost/2010/05/167192.php

---

## Comment 2

> Username: miralshah365  
> Created at: 2019-07-29 08:18:08 UTC  
> Url: https://github.com/boostorg/gil/issues/356#issuecomment-515893526  

> * What is the motivation?  
  
Most of the famous and most usable kernels are predefined and available on the internet are in the form of 2D kernels. If we want to use them with current implementation then it requires some other calculations to convert them into 1D kernels which can consume some time. On the other hand, not all the kernels are separable which means they can not be converted into 1D kernels.   
  
> * What problems is this plan going to solve?  
  
2D kernels will save programmers' time from some explicit calculations for the conversions from 2D to 1D. It will give more options to the programmer to choose from.  
  
> * What prevents kernel_1d from being a used in 2D context?  
  
The way all the kernels are not separable which prevents 2D kernels to be used in 1D kernels similarly some kernels can not be expanded to 2D from 1D  
  
> * What is your evaluation of `convolve`  recently added by @lpranam w.r.t. your plan?  
  
Although it convolves the image in both the direction still it uses the methods `covlolve_row/col`  which are the only compatible 1D kernel. I think it should be renamed to `convolve_1d` to avoid confusion in the future.  
> * What is lacking in the numeric's [2D separable convolution](https://github.com/boostorg/gil/blob/develop/include/boost/gil/extension/numeric/convolve.hpp#L29)?  
  
Every argument stops when we want to work with 2D kernels or a kernel is not separable.

---

## Comment 3

> Username: mloskot  
> Created at: 2019-07-29 10:01:52 UTC  
> Url: https://github.com/boostorg/gil/issues/356#issuecomment-515929225  

@miralshah365 This is a good summary. IMO, it's important a new feature is supported by a brief motivation pitch.
