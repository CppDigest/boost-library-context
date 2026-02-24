# #321 Comparison operator changed to `>` from `>=` for threshold [Merged]

> Username: miralshah365  
> Created at: 2019-07-05 13:17:28 UTC  
> Updated at: 2020-02-22 13:11:11 UTC  
> Merged at: 2019-07-10 22:48:13 UTC  
> Closed at: 2019-07-10 22:48:13 UTC  
> Url: https://github.com/boostorg/gil/pull/321  

### Description  
  
*Changed comparison operator in all the lambda function in `threshold.hpp` from `>=` to `>`.*  
  
### Reasons for the changes:  
1. To provide uniform behavior with other image processing libraries.  
2. Thresholds are boundary and boundaries are not included in the threshold values.  
3. See https://github.com/boostorg/gil/pull/321#issuecomment-508844056  
  
  
### Tasklist  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2019-07-05 19:04:08 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/321#pullrequestreview-258522350  

Thanks for fixing it.   
I've got one minor comment:

📁 include/boost/gil/image_processing/threshold.hpp

```diff
  87 |             [threshold_value, max_value](source_channel_t px) ->
  88 |-                 result_channel_t { return px >= threshold_value ? max_value : 0; });
  88 |+                 result_channel_t { return px > threshold_value ? max_value : 0; });
```

> Username: mloskot  
> Created_at: 2019-07-05 19:03:39 UTC  
> Updated_at: 2019-07-05 19:30:45 UTC  
> Url: https://github.com/boostorg/gil/pull/321#discussion_r300774870  

Would you mind reformatting all the lambdas to look this way?  
  
```  
[threshold_value, max_value](source_channel_t px) -> result_channel_t {  
    return px > threshold_value ? max_value : 0;  
});  
```  
  
This form is a bit more friendly for debugging, when one wants to set a breakpoint in the actual line  
`return px > threshold_value ? max_value : 0;`.


---

## Comment 2

> Username: mloskot  
> Created_at: 2019-07-05 19:21:13 UTC  
> Updated_at: 2019-07-05 19:25:59 UTC  
> Url: https://github.com/boostorg/gil/pull/321#issuecomment-508844056  

I'm labelling this as a BUG fix and here is why:  
  
You have provided valid reasons why to use `>` over `>=`.  
I would like to add one use case that presents why the use of `>=` is can be considered as a **bug**.  
  
------  
  
Let's consider the following grayscale image as input  
  
![numbers](https://user-images.githubusercontent.com/80741/60741660-99f79480-9f6a-11e9-9362-e66371743a1b.jpg)  
  
(Source: https://www.learnopencv.com/opencv-threshold-python-cpp/)  
  
Let's apply the current (`>=`) version of the binary thresholding to the  with the following _valid_ input values to separate features from a solid black background:  
  
```  
gil::threshold_binary(const_view(img), view(img_out), 0, 255);  
```  
  
The result is a completely **white image** with all pixels having value `255`, because the black background is being thresholded due to `0 >= 0 yields 255`.  
  
Whereas, expected and correct result is this binarization:  
  
![numbers-threshold-0-max-255](https://user-images.githubusercontent.com/80741/60741675-a4b22980-9f6a-11e9-91d3-6941a574f009.jpg)

---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2019-07-05 19:47:49 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/321#pullrequestreview-258532661  

LGTM! @miralshah365 Feel free to "Squash and merge".

---
