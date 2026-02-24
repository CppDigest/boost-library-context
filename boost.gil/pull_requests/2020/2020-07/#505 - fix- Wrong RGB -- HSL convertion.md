# #505 fix: Wrong RGB -> HSL convertion [Merged]

> Username: eugene-kulak  
> Created at: 2020-07-03 03:35:52 UTC  
> Updated at: 2022-06-24 06:45:58 UTC  
> Merged at: 2022-06-24 06:44:29 UTC  
> Closed at: 2022-06-24 06:44:29 UTC  
> Url: https://github.com/boostorg/gil/pull/505  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
Fixes wrong formula used to convert RGB to HSL color space,  
check correct algorithm here (https://www.niwa.nu/2013/05/math-behind-colorspace-conversions-rgb-hsl/)  
  
### References  
  
- https://github.com/boostorg/gil/issues/690  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-07-03 20:57:20 UTC  
> Url: https://github.com/boostorg/gil/pull/505#issuecomment-653675740  

Thanks for the patch.  
  
Could you tell how did you discover the issue, for what input, expected output, a MWE?  
In other words, it would be helpful if this PR includes a corresponding test case.

---

## Comment 2

> Username: eugene-kulak  
> Created_at: 2020-07-05 17:25:18 UTC  
> Url: https://github.com/boostorg/gil/pull/505#issuecomment-653915440  

Hi @mloskot , unfortunately, I don't use boost tests, but here are my GTests:  
  
```cpp  
#define rgb2hsl(r, g, b, h, s, l)                              \  
  {                                                            \  
    const float abs_error = 0.002;                             \  
    using boost::gil::hsl32f_pixel_t;                          \  
    using boost::gil::rgb8_pixel_t;                            \  
    using boost::gil::hsl_color_space::hue_t;                  \  
    using boost::gil::hsl_color_space::lightness_t;            \  
    using boost::gil::hsl_color_space::saturation_t;           \  
    rgb8_pixel_t rgb(r, g, b);                                 \  
    hsl32f_pixel_t hsl;                                        \  
                                                               \  
    color_convert(rgb, hsl);                                   \  
                                                               \  
    ASSERT_NEAR(h, get_color(hsl, hue_t()), abs_error);        \  
    ASSERT_NEAR(s, get_color(hsl, saturation_t()), abs_error); \  
    ASSERT_NEAR(l, get_color(hsl, lightness_t()), abs_error);  \  
  }  
  
#define hsl2rgb(h, s, l, r, g, b)                         \  
  {                                                       \  
    const float abs_error = 1;                            \  
    using boost::gil::blue_t;                             \  
    using boost::gil::green_t;                            \  
    using boost::gil::hsl32f_pixel_t;                     \  
    using boost::gil::red_t;                              \  
    using boost::gil::rgb8_pixel_t;                       \  
    hsl32f_pixel_t hsl(h, s, l);                          \  
    rgb8_pixel_t rgb;                                     \  
                                                          \  
    color_convert(hsl, rgb);                              \  
                                                          \  
    ASSERT_NEAR(r, get_color(rgb, red_t()), abs_error);   \  
    ASSERT_NEAR(g, get_color(rgb, green_t()), abs_error); \  
    ASSERT_NEAR(b, get_color(rgb, blue_t()), abs_error);  \  
  }  
  
TEST(ColorSpaceTest, RGBToHSL) {  
  // black  
  rgb2hsl(0, 0, 0, 0, 0, 0);  
  
  // white  
  rgb2hsl(255, 255, 255, 0, 0, 1);  
  
  // red  
  rgb2hsl(255, 0, 0, 0, 1, 0.5);  
  
  // lime  
  rgb2hsl(0, 255, 0, 2 / 6.f, 1, 0.5);  
  
  // blue  
  rgb2hsl(0, 0, 255, 4 / 6.f, 1, 0.5);  
  
  // yellow  
  rgb2hsl(255, 255, 0, 1 / 6.f, 1, 0.5);  
  
  // cyan  
  rgb2hsl(0, 255, 255, 3 / 6.f, 1, 0.5);  
  
  // magenta  
  rgb2hsl(255, 0, 255, 5 / 6.f, 1, 0.5);  
  
  // silver  
  rgb2hsl(191, 191, 191, 0, 0, 0.75);  
  
  // gray  
  rgb2hsl(128, 128, 128, 0, 0, 0.5);  
  
  // maroon  
  rgb2hsl(128, 0, 0, 0, 1, 0.25);  
  
  // olive  
  rgb2hsl(128, 128, 0, 1 / 6.f, 1, 0.25);  
  
  // green  
  rgb2hsl(0, 128, 0, 2 / 6.f, 1, 0.25);  
  
  // purple  
  rgb2hsl(128, 0, 128, 5 / 6.f, 1, 0.25);  
  
  // teal  
  rgb2hsl(0, 128, 128, 3 / 6.f, 1, 0.25);  
  
  // navy  
  rgb2hsl(0, 0, 128, 4 / 6.f, 1, 0.25);  
}  
  
TEST(ColorSpaceTest, HSLToRGB) {  
  // black  
  hsl2rgb(0, 0, 0, 0, 0, 0);  
  
  // white  
  hsl2rgb(0, 0, 1, 255, 255, 255);  
  
  // red  
  hsl2rgb(0, 1, 0.5, 255, 0, 0);  
  
  // lime  
  hsl2rgb(2 / 6.f, 1, 0.5, 0, 255, 0);  
  
  // blue  
  hsl2rgb(4 / 6.f, 1, 0.5, 0, 0, 255);  
  
  // purple  
  hsl2rgb(5 / 6.f, 1, 0.25, 128, 0, 128);  
  
  // teal  
  hsl2rgb(3 / 6.f, 1, 0.25, 0, 128, 128);  
  
  // navy  
  hsl2rgb(4 / 6.f, 1, 0.25, 0, 0, 128);  
}  
  
```

---

## Review 3 [Approved]

> Username: lpranam  
> Created_at: 2022-06-24 05:24:32 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/505#pullrequestreview-1017995230  

Looks good. Would be nice to add those tests now we don't use boost test so should be easy. Let us know if you can add or not. @eugene-kulak

---

## Comment 4

> Username: mloskot  
> Created_at: 2022-06-24 06:43:31 UTC  
> Url: https://github.com/boostorg/gil/pull/505#issuecomment-1165255330  

@lpranam I think we will have to add the tests on our own  
I'm merging this, so this can be followed with porting of the GTest tests - https://github.com/boostorg/gil/issues/690

---
