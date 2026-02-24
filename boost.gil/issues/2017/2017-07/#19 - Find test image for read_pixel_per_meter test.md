# #19 - Find test image for read_pixel_per_meter test [Closed]

> Username: christian-henning  
> Created at: 2017-07-02 14:54:49 UTC  
> Updated at: 2018-03-19 11:28:50 UTC  
> Closed at: 2018-03-19 11:28:50 UTC  
> Url: https://github.com/boostorg/gil/issues/19  

Cannot find original test image.

---

## Comment 1

> Username: chhenning  
> Created at: 2018-03-18 16:18:40 UTC  
> Url: https://github.com/boostorg/gil/issues/19#issuecomment-374012035  

This is not a showstopper.

---

## Comment 2

> Username: mloskot  
> Created at: 2018-03-18 22:02:25 UTC  
> Updated at: 2018-03-18 22:02:40 UTC  
> Url: https://github.com/boostorg/gil/issues/19#issuecomment-374054722  

Hmm, isn't it this image here [io/test_images/png/EddDawson/36dpi.png](https://github.com/boostorg/gil/blob/develop/io/test_images/png/EddDawson/36dpi.png) as per this post during the review https://lists.boost.org/Archives/boost/2010/12/173801.php?

---

## Comment 3

> Username: chhenning  
> Created at: 2018-03-18 22:22:54 UTC  
> Url: https://github.com/boostorg/gil/issues/19#issuecomment-374057633  

You're correct! Currently the test is commented out:  
  
https://github.com/boostorg/gil/blob/develop/io/test/png_read_test.cpp#L130

---

## Comment 4

> Username: mloskot  
> Created at: 2018-03-18 22:26:21 UTC  
> Url: https://github.com/boostorg/gil/issues/19#issuecomment-374058105  

So, looks like it can just be uncommented and voila, no? :)

---

## Comment 5

> Username: chhenning  
> Created at: 2018-03-18 22:34:50 UTC  
> Url: https://github.com/boostorg/gil/issues/19#issuecomment-374059322  

Yes!
