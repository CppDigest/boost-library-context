# #323 - [gsoc2019] color_spaces_are_compatible requirement in threshold too strict [Closed]

> Username: mloskot  
> Created at: 2019-07-05 19:44:49 UTC  
> Updated at: 2020-02-22 13:10:23 UTC  
> Closed at: 2019-07-10 10:20:42 UTC  
> Url: https://github.com/boostorg/gil/issues/323  

### Description  
  
The requirement that both views, source and destination, are based on pixels with compatible (aka equal) color spaces ~~turns out to~~ may be too strict:  
  
https://github.com/boostorg/gil/blob/ee169ef104528b99e47186a01414b02e46416812/include/boost/gil/image_processing/threshold.hpp#L37-L41  
  
The threshold algorithm transform source view into destination view applying pixel-wise transformation, so it is implemented in terms of `static_transform`.  
  
Now, the `static_transform` is not as strict but "is constrained by the size of destination color base" what has been explained and illustrated by #319 in details.  
  
It means that acceptable input is:  
  
1. Both views, source and destination, have compatible (equal) color space pixels.  
2. Color space of source view is **wider**, color space of destination is **narrower**.  
   For example source is RGB (or CMYK), destination is Gray (or RGB), and similar combinations.  
  
### Proposal  
  
~~In my opinion~~ We need to decide if this `ColorSpacesCompatibleConcept` requirement should be removed entirely. There already is static assertion in place that will be triggered if `static_transform` is called for pixels with color space incompatible for the transformation operation:  
  
https://github.com/boostorg/gil/blob/ee169ef104528b99e47186a01414b02e46416812/include/boost/gil/color_base_algorithm.hpp#L80-L81  
  
Alternatively, the  `ColorSpacesCompatibleConcept`  could be replaced with a new `static_assert` that performs very similar check as the one above.  
  
### References  
  
- [[threshold] Global binarization vs multi-channel binarization](https://lists.boost.org/boost-gil/2019/07/0258.php) thread on ML

---

## Comment 1

> Username: mloskot  
> Created at: 2019-07-06 18:18:59 UTC  
> Updated at: 2019-07-06 18:42:07 UTC  
> Url: https://github.com/boostorg/gil/issues/323#issuecomment-508944967  

From Gitter on [July 6, 2019 4:40 PM](https://gitter.im/boostorg/gil?at=5d20b2d59a8a5d3cf4a89267)  
> @mloskot I went through #323. I am still a bit confused about how static_transform will behave with different types of source and destination. i.e: RBG to gray  
  
@miralshah365 The reasons why I suggest to discuss via comments this issue (or the thread on boost-gil ML) are:  
1. The issue is not an obvious and there are several aspects to discuss  
2. The discussion will lead to decisions that will affect design and behaviour of the thresholding  
3. In future, it may be important, if not critical, to have that discussion leading to the decisions archived and available to understand where the rationale came from.  
  
### Problem with static_transform  
  
The motivation behind archiving such important discussions is to avoid situation like the one at hand with understanding the `static_transform`, when we are asking questions and trying to understand why original authors implemented it as they did:  
  
1. What is the rationale behind allowing wider to narrower color space transformation?  
2. What is the point behind allowing transformation `RGB` to `Gray` that takes the `R` channel and outputs as `Gray`? Similarly, `CMYK` to `Gray` that takes the `C` and outputs as `Gray` or `CMYK` to `RGB` as `C -> R`, `M -> G`, `Y -> Y`, etc.?  
3. Why not require both, source and destination, to be of compatible/equal color spaces?  
  
### Task for this issue  
  
So, @miralshah365 , our task is to answer some questions that will affect design/behaviour of the `threshold` functions:  
  
1. Is the wider to narrower color space transformation of `static_transform` behaviour by design or accidental?  
2. If we the `ColorSpacesCompatibleConcept ` requirement in the `threshold` functions is removed, then those functions will publicly exhibit/follow behaviour of `static_transform`.  
   - Do we want to let that detail leak out?  
3. Do we want to design the `threshold` functions to accept only compatible color spaces and keep the `static_transform` behaviour as internal implementation detail?  
   - This option seems reasonable and future-proof. We can imagine situation that `static_transform` is replaced with some other (e.g. more optimal) transformation implementation.  
   - In that case, it is important to not to let the `static_transformation` behaviour leak out and be part of `threshold` function interface.  
4. If the `threshold` functions allow only compatible color spaces, how do we design their behaviour for compatible but multi-channel source/destination?  
   - For example, do we want to allow `threshold(RGB, RGB)`, `threshold(RGBA, RGBA)`, `threshold(CMYK, CMYK)`?  
   - If we do, waht we want those cases to behave like?

---

## Comment 2

> Username: mloskot  
> Created at: 2019-07-06 21:10:32 UTC  
> Updated at: 2019-07-06 21:21:20 UTC  
> Url: https://github.com/boostorg/gil/issues/323#issuecomment-508953836  

From Gitter on [July 6, 2019 8:50 PM](https://gitter.im/boostorg/gil?at=5d20ed81ff78641c1ca1f243)  
> Actually when i was writing new test i tried rgb source and gray dest by removing concept check but results were not good  
  
@miralshah365 You need to define "not good"  
  
```cpp  
#include <boost/gil/extension/io/png.hpp>  
#include <boost/gil/image_processing/threshold.hpp>  
#include <boost/gil.hpp>  
using namespace boost::gil;  
  
int main()  
{  
    rgba8_image_t img;  
    read_image("numbers.png",img, png_tag{});  
    gray8_image_t img_out(img.dimensions());  
    boost::gil::threshold_binary(const_view(img), view(img_out), 0, 255);  
    write_view("numbers-out-threshold-zero.png", view(img_out), png_tag{});  
}  
```  
  
![image](https://user-images.githubusercontent.com/80741/60761165-3e4d0a00-a043-11e9-8d4b-6655a6a33a65.png)  
  
------  
  
Think of my suggestion as the [Syntax-First Library Development](http://www.drdobbs.com/database/a-simple-oracle-call-interface/184405930?pgno=2),  or our earlier discussion about [thresholding API design](https://lists.boost.org/boost-gil/2019/05/0211.php).  
  
We need to take the syntax `threshold_binary(input, output, threshold, max_value)` and given  
  
- input can be Gray, RGB, RGBA, CMYK, ...  
- output can be Gray, RGB, RGBA, CMYK, ...  
  
we need to consider and decide what we want the threshold_binary do for Cartesian product of the input/output, by our design, and **without considering** how `static_transform` or any other implementation behaves.  
  
We need to make **conscious decision** what we want the function do for the possible arguments.  
And, we need to cover that with tests, expecting such and such input succeeds, such and such input fails.

---

## Comment 3

> Username: mloskot  
> Created at: 2019-07-07 19:51:20 UTC  
> Updated at: 2019-07-10 04:02:34 UTC  
> Url: https://github.com/boostorg/gil/issues/323#issuecomment-509025917  

I've been reflecting on the overall issue and I agree with what you responded on the mailing list, that we are close to the optimal solution.  
  
The OpenCV [threshold](https://docs.opencv.org/3.4/d7/d1b/group__imgproc__misc.html#gae8a4a146d1ca78c626a53577199e9c57) function specifies:  
  
> src | input array (multiple-channel, 8-bit or 32-bit floating point).  
> dst | output array of the same size and type and the same number of channels as src.  
  
and  
  
> Otsu's and Triangle methods are implemented only for **8-bit single-channel** images.  
  
@miralshah365 Does it sound to you like a reasonable choice too?  
  
I think this is aligned with what you've explained in your response to [[threshold] Global binarization vs multi-channel binarization"](https://lists.boost.org/boost-gil/2019/07/0257.php):  
  
> Multi-channel binarization is performed by performing threshold operation  
> on each channel and returning a view with each channel independently  
> thresholded.   
> ...  
> threshold_* does not depend on dst_view type but src_view and dst_view must  
> be of the same type.   
  
So, this all sounds correct to me too.  
  
The only minor concern I'm having now is the use of `gil_function_requires` for `ColorSpacesCompatibleConcept`, because such check optional and only enabled if `BOOST_GIL_USE_CONCEPT_CHECK` defined:  
  
https://github.com/boostorg/gil/blob/74c497c36fe00d5597702e7d1349848cf9c25a67/include/boost/gil/concepts/concept_check.hpp#L42  
  
I'd simply add an assertion  
  
```cpp  
static_assert(color_spaces_are_compatible  
<  
    typename color_space_type<SrcView>::type,  
    typename color_space_type<DstView>::type  
>::value, "Source and destination views must have pixels with the same color space");  
```  
  
What do you think?
