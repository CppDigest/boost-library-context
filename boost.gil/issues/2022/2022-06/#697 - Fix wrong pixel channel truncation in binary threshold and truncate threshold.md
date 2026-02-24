# #697 - Fix wrong pixel channel truncation in binary threshold and truncate threshold [Open]

> Username: marco-langer  
> Created at: 2022-06-27 14:28:19 UTC  
> Updated at: 2024-08-20 13:02:29 UTC  
> Url: https://github.com/boostorg/gil/issues/697  

### Actual behavior  
`gil::threshold_truncate()` and `gil::threshold_binary()` from gil/image_processing/truncate.hpp employ a hard-coded value of 0 for the channel minimum value and a value of `std::numeric_limits<channel_t>::max()` for the maximum value. This works only for unsigned integer channel types such as `gil::rgb8_pixel_t` and `gil::gray8_pixel_t`, but does not work for signed channel types and also not for floating point types whose value range is limited from 0.0 to 1.0.  
  
Edit: Otsu thresholding also employs std::numeric_limits, which is invalid for scoped channels.  
  
### Expected  behavior  
  
Correct channel value truncation according to the channel min/max values.

---

## Comment 1

> Username: mloskot  
> Created at: 2022-06-27 16:36:01 UTC  
> Url: https://github.com/boostorg/gil/issues/697#issuecomment-1167584286  

I may vaguely recall that I agreed with @miralshah365 to begin with support unsigned integer channel types only.  
There should be at least a static assertion though as a documentation of such limitation.  
  
It would be nice to have this fixed/improved indeed.

---

## Comment 2

> Username: marco-langer  
> Created at: 2022-06-27 16:46:13 UTC  
> Url: https://github.com/boostorg/gil/issues/697#issuecomment-1167597623  

As far as I see there is currently only one static assert making sure that input and output views are compatible.
