# #690 - Add tests for PR #505 with RGB to HSL fix [Closed]

> Username: mloskot  
> Created at: 2022-06-24 06:42:26 UTC  
> Updated at: 2022-06-25 16:51:01 UTC  
> Closed at: 2022-06-25 16:51:01 UTC  
> Url: https://github.com/boostorg/gil/issues/690  

Contributor of #505 posted GTest-based tests in https://github.com/boostorg/gil/pull/505#issuecomment-653915440, copied below.  
We need to port this to Boost.LightweightTest  
  
```c++  
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
