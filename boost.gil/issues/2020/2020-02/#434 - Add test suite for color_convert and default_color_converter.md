# #434 - Add test suite for color_convert and default_color_converter [Open]

> Username: mloskot  
> Created at: 2020-02-15 20:13:37 UTC  
> Updated at: 2021-02-06 12:20:35 UTC  
> Url: https://github.com/boostorg/gil/issues/434  

We need to start a detailed test cases coverage for color conversion algorithms:  
  
- Add `test/core/pixel/color_convert.cpp`  
- Use Boost.Test to define test cases using `BOOST_AUTO_TEST_CASE` or `BOOST_AUTO_TEST_CASE_TEMPLATE`  
- Add test cases for each pre-defined specialisations of converters  
- Covert byte-based as well as bit-aligned and packed pixel types  
  
---  
  
It's a good idea to exercise the `color_convert` and `default_color_converter` using available interfaces, for example  
  
```cpp  
gil::rgb8_pixel_t red(255, 0, 0);  
... red2;  
  
// three equivalent conversions  
gil::color_convert(red1, red2);  
gil::default_color_converter()(red1, red2);  
red2 = gil::color_convert_deref_fn<gil::rgb8_ref_t, ...>()(red1);  
```

---

## Comment 1

> Username: pankaj-2407  
> Created at: 2021-02-06 12:18:29 UTC  
> Url: https://github.com/boostorg/gil/issues/434#issuecomment-774468587  

Hi,  
I would like to work on this issue.

---

## Comment 2

> Username: lpranam  
> Created at: 2021-02-06 12:19:43 UTC  
> Url: https://github.com/boostorg/gil/issues/434#issuecomment-774469218  

Feel free to take on this. No need for permission. :D

---

## Comment 3

> Username: pankaj-2407  
> Created at: 2021-02-06 12:20:35 UTC  
> Url: https://github.com/boostorg/gil/issues/434#issuecomment-774469294  

Thankyou @lpranam
