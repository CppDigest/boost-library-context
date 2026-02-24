# #341 Add adaptive threshold algorithm using mean method [Merged]

> Username: miralshah365  
> Created at: 2019-07-20 20:26:31 UTC  
> Updated at: 2020-03-11 04:59:31 UTC  
> Merged at: 2019-07-25 20:06:44 UTC  
> Closed at: 2019-07-25 20:06:44 UTC  
> Url: https://github.com/boostorg/gil/pull/341  

### Description  
New threshold adaptive method added.   
  
This method allows the user to set threshold values according to neighbors. The threshold value is found by calculating the mean of all the neighbors including pixel itself. This mean value is then treated as a threshold and the binary threshold is performed.  
  
  
### References  
  
- Implements #315   
  
### Tasklist  
  
- [ ] Add test case(s) to be added as part of #353   
- [x] Ensure all CI builds pass (https://github.com/boostorg/gil/pull/341#issuecomment-515184207)   
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2019-07-20 20:34:17 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/341#pullrequestreview-264510399  

@miralshah365 First round of review that only addresses the run-time failure you mentioned on Gitter.

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 386 |+                         col_boundary >= 0 && col_boundary < src_view.width())
 387 |+                     {
 388 |+                         dst_view(view_row, view_col) =
```

> Username: mloskot  
> Created_at: 2019-07-20 20:33:36 UTC  
> Updated_at: 2019-07-25 05:40:24 UTC  
> Url: https://github.com/boostorg/gil/pull/341#discussion_r305592197  

@miralshah365  First, let's address the run-time failure you mentioned on Gitter.  
  
Please, add these run-time checks before the `dst_view(view_row, view_col)` assignment:  
  
```  
BOOST_ASSERT(row_boundary < src_view.height());  
BOOST_ASSERT(col_boundary < src_view.height());  
BOOST_ASSERT(view_row < dst_view.height());  
BOOST_ASSERT(view_col < dst_view.width());  
```  
  
AFAICT, indices calculation is incorrect and leads to writing memory beyond valid range which eventually leads to heap corruption (of the image memory) leading to run-time failures you are observing.

> Username: mloskot  
> Created_at: 2019-07-20 22:38:56 UTC  
> Updated_at: 2019-07-25 05:40:24 UTC  
> Url: https://github.com/boostorg/gil/pull/341#discussion_r305595652  

@miralshah365 As you have spotted already, I made mistake in one of the assertions above  
`BOOST_ASSERT(col_boundary < src_view.height());`   
should read  
BOOST_ASSERT(col_boundary < src_view.width());  
  
However, image heap corruption must be due to a bug somewhere else.

> Username: mloskot  
> Created_at: 2019-07-20 22:42:06 UTC  
> Updated_at: 2019-07-25 05:40:24 UTC  
> Url: https://github.com/boostorg/gil/pull/341#discussion_r305595730  

@miralshah365 Well, it seems indices of sources and destination view are used as mismatched:  
  
```  
for (std::ptrdiff_t view_row = 0; view_row < src_view.height(); ++view_row)  
```  
  
then  
  
```  
dst_view(view_row, view_col)  
```  
  
where `view_row` is in range of `src_view`, isn't it?  
  
If both dimensions are expected to be equal, there should be assertions to document that.

> Username: miralshah365  
> Created_at: 2019-07-20 22:46:07 UTC  
> Updated_at: 2019-07-25 05:40:24 UTC  
> Url: https://github.com/boostorg/gil/pull/341#discussion_r305595825  

@mloskot but in the function I have passes one copy of `src_view`. look at line 432 of `threshold.hpp`

> Username: mloskot  
> Created_at: 2019-07-20 23:29:46 UTC  
> Updated_at: 2019-07-25 05:40:24 UTC  
> Url: https://github.com/boostorg/gil/pull/341#discussion_r305596595  

@miralshah365 Compare this addressing here  
```  
dst_view(view_row, view_col)  
```  
and  
```  
src_view(row_boundary, col_boundary)  
```  
  
with this meaning of the arguments:  
  
 https://github.com/boostorg/gil/blob/74c497c36fe00d5597702e7d1349848cf9c25a67/include/boost/gil/image_view.hpp#L159  
  
This would have been caught much easier if we had these assertions in `image_view::operator()`  :-)  
  
```  
    reference operator()(x_coord_t x, y_coord_t y)const  
    {  
        BOOST_ASSERT(x < width());  
        BOOST_ASSERT(y < height());  
        return _pixels(x,y);  
    }  
```


---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2019-07-22 23:40:03 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/341#pullrequestreview-265109650  

@miralshah365 I noticed a memory leak

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 378 |+     {
 379 |+         float *mean = new float[kernel_size];
 380 |+         std::fill_n(mean, kernel_size, 1.0f/kernel_size);
```

> Username: mloskot  
> Created_at: 2019-07-22 23:39:41 UTC  
> Updated_at: 2019-07-25 05:40:24 UTC  
> Url: https://github.com/boostorg/gil/pull/341#discussion_r306076550  

This looks like a memory leak to me.  
I'd suggest to replace the two lines with something like this:  
  
```  
std::vector<float> mean_kernel_values(kernel_size, 1.0f/kernel_size);  
kernel_1d<float> kernel(mean_kernel_values.begin(), kernel_size, kernel_size/2);  
```


---

## Comment 3

> Username: mloskot  
> Created_at: 2019-07-23 00:32:59 UTC  
> Url: https://github.com/boostorg/gil/pull/341#issuecomment-514006219  

@miralshah365   
I've been playing with the algorithm against this little funny sample of `16x16` pixel greyscale gradient background with two 'barely visible rectangles.  
You can download it here ![16x16rectangles](https://user-images.githubusercontent.com/80741/61673455-d9a2d800-acef-11e9-8a9e-bda70fff15e3.png).  
  
![image](https://user-images.githubusercontent.com/80741/61673736-f7247180-acf0-11e9-97e2-eeaacfa1f38d.png)  
  
### OpenCV  
  
The 16x16 sample above passed through the adaptive mean threshold by OpenCV:  
  
```python  
import cv2  
import numpy as np  
import matplotlib.pyplot as plt  
path = 'D:\\workshop\\opencv\\'  
path_original = path + "rectangles.png"  
  
img = cv2.imread(path_original, 0)  
  
ret, th1 = cv2.threshold(img, 127, 255, cv2.THRESH_BINARY)  
th2 = cv2.adaptiveThreshold(  
    img, 255, cv2.ADAPTIVE_THRESH_MEAN_C, cv2.THRESH_BINARY, 11, 0)  
th3 = cv2.adaptiveThreshold(  
    img, 255, cv2.ADAPTIVE_THRESH_GAUSSIAN_C, cv2.THRESH_BINARY, 11, 0)  
  
titles = ['Input', 'Global Thresholding (t=127)',  
          'Adaptive Mean', 'Adaptive Gaussian']  
images = [img, th1, th2, th3]  
for i in range(4):  
    plt.subplot(2, 2, i+1), plt.imshow(images[i], 'gray')  
    plt.title(titles[i])  
    plt.xticks([]), plt.yticks([])  
plt.show()  
```  
  
identifies both rectangles:  
  
![image](https://user-images.githubusercontent.com/80741/61673577-62217880-acf0-11e9-8801-93d7174c2d92.png)  
  
### GIL  
  
Using `threshold_adaptive`:  
  
```cpp  
std::string image_dir = "d:\\workshop\\opencv\\";  
{  
    gray8_image_t img;  
    read_image(image_dir + "rectangles.png", img, png_tag{});  
    gray8_image_t img_out(img.dimensions());  
  
    boost::gil::threshold_adaptive(const_view(img), view(img_out), 11);  
    write_view(image_dir + "out-threshold-adaptive.png", view(img_out), png_tag{});  
}  
```  
  
identifies only one, the upper rectangle:  
  
![image](https://user-images.githubusercontent.com/80741/61673503-00610e80-acf0-11e9-9192-cd028e8231c0.png)  
  
Here is dump of the convoluted view generated with this  
  
```cpp  
write_view("d:\\workshop\\opencv\\out-threshold-adaptive-convoluted-view.png", temp_view, png_tag{});  
```  
  
![image](https://user-images.githubusercontent.com/80741/61673885-a103fe00-acf1-11e9-87f9-aba756f51012.png)  
  
Unfortunately, I have no way to generate equivalent for OpenCV.  
  
### Conclusion  
  
There still may be some fine tuning of the algorithm necessary.  
At least, it may be worth to test it a bit more, also with your impl. of convolution.

---

## Comment 4

> Username: miralshah365  
> Created_at: 2019-07-23 05:29:17 UTC  
> Url: https://github.com/boostorg/gil/pull/341#issuecomment-514059755  

That's because of kernel size and image size are too close and in such cases what scheme we chose for the border is really important. OpenCV's default scheme is  [this](https://docs.opencv.org/3.1.0/d2/de8/group__core__array.html#ga209f2f4869e304c82d07739337eae7c5) while GIL extends the border with `zero`s  
  
I created the same type of image but in a larger size (3000x3000 pixels)   
![img2](https://user-images.githubusercontent.com/37846212/61684749-0ed31880-ad38-11e9-95ba-74d2654b9f3c.png)  
  
and tests on this image:  
  
OpenCV output:  
![aimg2](https://user-images.githubusercontent.com/37846212/61684813-42ae3e00-ad38-11e9-87fb-68603591abdc.png)  
  
Boost.GIL output:  
![out-threshold-adaptive](https://user-images.githubusercontent.com/37846212/61684829-4fcb2d00-ad38-11e9-8574-d888bc4fcfec.png)  
  
Somehow they are inverted I'll investigate that behavior.

---

## Comment 5

> Username: mloskot  
> Created_at: 2019-07-23 22:21:20 UTC  
> Updated_at: 2019-07-24 06:53:00 UTC  
> Url: https://github.com/boostorg/gil/pull/341#issuecomment-514405289  

@miralshah365 Here is comparison of the `rectangles.png` passed through the `threshold_adaptive(kernel_size=11)` and `convolve` called with all the supported options.  
Shortly, `convolve_option_extend_constant` gives most similar output to the OpenCV.  
  
------  
  
LHS - `threshold_adaptive` output image, RHS - `convolve` result image.  
  
![image](https://user-images.githubusercontent.com/80741/61750501-e255e580-ada5-11e9-8922-48c16f551d50.png)  
  
![image](https://user-images.githubusercontent.com/80741/61750535-f4d01f00-ada5-11e9-84a9-12134e602108.png)  
  
------  
  
Interestingly, OpenCV for `ADAPTIVE_THRESH_MEAN_C` uses `BORDER_REPLICATE|BORDER_ISOLATED`  (see [imgproc/src/thresh.cpp:1666-1668](https://github.com/opencv/opencv/blob/199ddff13b73071529a42cff97ee8286cfc15d02/modules/imgproc/src/thresh.cpp#L1666-L1668)).  
  
@miralshah365  This OpenCV's boundary mode is quite alike the `convolve_option_extend_constant` which also replicates boundary values, right? For the mean filter, the replication does make sense, doesn't it?

---

## Review 6 [Changes requested]

> Username: mloskot  
> Created_at: 2019-07-23 22:37:08 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/341#pullrequestreview-265712825  

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 424 |+             detail::adaptive_impl<source_channel_t, result_channel_t>(src_view, temp_conv, dst_view,
 425 |+                 [max_value](source_channel_t px1, source_channel_t px2) -> result_channel_t
 426 |+                     { return px1 >= px2 ? max_value : 0; });
```

> Username: mloskot  
> Created_at: 2019-07-23 22:36:08 UTC  
> Updated_at: 2019-07-25 05:40:24 UTC  
> Url: https://github.com/boostorg/gil/pull/341#discussion_r306562077  

1. This is a regular binary threshold, so shouldn't `px1 >= px2` read `px1 > px2`?  
  
2. Could you also rename?  
  
   - `px1` to just `px` or `pixel`    
   - `px2` to `threshold`  
  
   While reading this, I constantly have to double-check in memory which is which, which comes  from source pixels and which from convolved pixels as thresholds :-)

> Username: mloskot  
> Created_at: 2019-07-24 22:23:10 UTC  
> Updated_at: 2019-07-25 05:40:24 UTC  
> Url: https://github.com/boostorg/gil/pull/341#discussion_r307046319  

Additional notes on the point 1. above posted in https://github.com/boostorg/gil/pull/341#issuecomment-514817002


---

## Review 7 [Changes requested]

> Username: mloskot  
> Created_at: 2019-07-23 22:40:44 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/341#pullrequestreview-265714226  

📁 include/boost/gil/image_processing/threshold.hpp

```diff
 416 |+             src_view, kernel, temp_view
 417 |+         );
 418 |+         convolve_cols<pixel<float, typename SrcView::value_type::layout_t>>(
```

> Username: mloskot  
> Created_at: 2019-07-23 22:40:39 UTC  
> Updated_at: 2019-07-25 05:40:24 UTC  
> Url: https://github.com/boostorg/gil/pull/341#discussion_r306563201  

@miralshah365 Since #347, you should now be able to replace `convolve_rows` and `convolve_cols` with single:  
  
```  
convolve<pixel<float, typename SrcView::value_type::layout_t>>(src_view, kernel, temp_view);  
```


---

## Comment 8

> Username: mloskot  
> Created_at: 2019-07-24 22:05:14 UTC  
> Url: https://github.com/boostorg/gil/pull/341#issuecomment-514817002  

(Related to my earlier comment https://github.com/boostorg/gil/pull/341#discussion_r306562077)  
  
@miralshah365  From [your yesterday comment on Gitter](https://gitter.im/boostorg/gil?at=5d379c0bb2db751dabb98df8):  
> - correct output is what OpenCV produces with `C=0`  
> - correct output is **white rectangles on black background**  
  
I don't think there is a single correct output because for such sharp edge cases (i.e. dark gray features on light gray background), thresholding result does depend on value of `C1:  
  
From adaptive threshold description at http://homepages.inf.ed.ac.uk/rbf/HIPR2/adpthrsh.htm  
> if the threshold employed is not the mean, but (mean-C), where C is a constant.  
> Using this statistic, all pixels which exist in a uniform neighborhood (e.g. along the margins) are set to background.  
  
Assuming that [OpenCV defaults to C=5](https://github.com/opencv/opencv/blob/199ddff13b73071529a42cff97ee8286cfc15d02/modules/imgproc/include/opencv2/imgproc/imgproc_c.h#L833), one may argue that 'correct' output is **black rectangles on white background**. This is what OpenCV produces from our `3000x3000` test image with the two rectangles for any `C>0`. So, since 'correct' depends on algorithm parameters, I wouldn't seek for a single correct answer.  
  
OpenCV:  
  
- `C > 0`: **black rectangles** on white background  
- `C = 0`: **white rectangles** on black background  
  
GIL:  
  
- `px1 >= px2 ? max_value : 0`: **black rectangles** on white background  
- `px1 >  px2 ? max_value : 0`: **white rectangles** on black background  
  
In other words, equivalent threshold tests are:  
  
* **black rectangles**   
  
    ```  
    px1 >= px2      ? max_value : 0  
    px1 > (px2 - 5) ? max_value : 0  
    ```  
  
* **white rectangles**  
  
    ```  
    px1 > px2       ? max_value : 0  
    px1 > (px2 - 0) ? max_value : 0  
    ```  
  
So, the major difference between behaviour of OpenCV and GIL that I can see is this flexibility due to the extra constant parameter. This is a common approach, see also https://imagej.net/Auto_Local_Threshold for the Mean formula it uses.  
  
@miralshah365 Your algorithm could also allow such parametrization.  
  
### Alternative mean test using offset  
  
Let's an alternative threshold test using `C` constant as mean offset:  
  
`std::abs(px1 - px2) < C ? max_value : 0`  
  
Although I don't think it is common approach and rather fine-tuned/specific to cetain applications, its properties are interesting, from http://www.roborealm.com/help/Adaptive_Threshold.php:  
> This helps to ensure stability of the white pixels by ensuring that they white pixels are higher than the mean by X amount.  
> A low offset value will cause some pixels to vibrate between black and white if they are near the intensity edge.   
  
If you try it out for our test case, you should see that it produces **thicker and gradual outline** of each rectangle which closely reflects the mean values near the edges of rectangles (produced by the `convolve` with the mean kernel), which are gradual as well:  
  
![image](https://user-images.githubusercontent.com/80741/61779721-7e610a80-ae01-11e9-8e7e-d15e15fdca03.png)

---

## Review 9 [Approved]

> Username: mloskot  
> Created_at: 2019-07-25 13:38:55 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/341#pullrequestreview-266630950  

@miralshah365  Good job! Please, merge whenever you like (assuming CI-s are green, of course).  
  
![image](https://user-images.githubusercontent.com/80741/61878916-41287580-aef2-11e9-96a4-17cafeba4831.png)  
  
------  
  
More tests will come as part of separate task #353 along with the fine-tuning of the algorithm itself, if we discover and agree as necessary.

---

## Comment 10

> Username: mloskot  
> Created_at: 2019-07-25 19:35:10 UTC  
> Url: https://github.com/boostorg/gil/pull/341#issuecomment-515184207  

@miralshah365 Travis CI for `boostorg/` must be having big queue, so we may be waiting 12, 24, 48 ... hours, unfortunately. However, I see that your private Travis CI has built this branch green: https://travis-ci.org/miralshah365/gil/builds/563401955, so we should be safe to assume it's all good.  
  
If you want, I can merge this PR without waiting for the Boost's Travis CI. Shall I do it?

---
