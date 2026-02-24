# #406 - Invalid conversion from RGB8 to CMYK32 [Closed]

> Username: awulkiew  
> Created at: 2019-11-29 02:30:25 UTC  
> Updated at: 2020-04-10 17:11:34 UTC  
> Closed at: 2020-04-10 17:11:34 UTC  
> Url: https://github.com/boostorg/gil/issues/406  

### Actual behavior  
  
While converting to CMYK32, if a channel's value is close to max `uint32` value the result is `0`. It's because internally in the conversion function `default_color_converter_impl<rgb_t,cmyk_t>::operator()` in [color_convert.hpp](https://github.com/boostorg/gil/blob/5611bd58071873d6346fe3cc05fe86c72f697717/include/boost/gil/color_convert.hpp#L140) file the channel value is represented as `float` which is unable to represent the high end of the uint32 range well enough.  
  
E.g. when red pixel is converted the value of magenta channel calculated like this:  
```  
((get_color(dst,magenta_t()) - get_color(dst,black_t()))*x1)  
```  
  
https://github.com/boostorg/gil/blob/5611bd58071873d6346fe3cc05fe86c72f697717/include/boost/gil/color_convert.hpp#L154  
  
the value is `4.29496730e+09` while max `uint32` is `4294967295`. When this value is converted from `float` to `uint32` the result is `0`.  
  
Changing the type of `x1` from `float` to `double` fixes the issue. Or maybe the expressions could be modified somehow to avoid FP numbers altogether?  
  
### Expected  behavior  
  
Generate this:  
  
![image](https://user-images.githubusercontent.com/1226951/69838805-6e48b400-1255-11ea-9bd4-3cb0099c8eb4.png)  
  
instead of this:  
  
![image](https://user-images.githubusercontent.com/1226951/69838874-a3ed9d00-1255-11ea-8091-796b6df5430a.png)  
  
### C++ Minimal Working Example  
  
```cpp  
boost::gil::rgb8_image_t img2;  
boost::gil::read_image("b.bmp", img2, boost::gil::bmp_tag());  
  
boost::gil::cmyk32_image_t img11(img2.dimensions());  
boost::gil::copy_pixels(  
    boost::gil::color_converted_view<boost::gil::cmyk32_pixel_t>(boost::gil::view(img2)),  
    boost::gil::view(img11));  
```  
  
### Environment  
  
Visual Studio 2017

---

## Comment 1

> Username: simmplecoder  
> Created at: 2020-03-30 06:41:07 UTC  
> Updated at: 2020-03-30 06:41:44 UTC  
> Url: https://github.com/boostorg/gil/issues/406#issuecomment-605812374  

### Report  
  
Confirmed. This is indeed the case, and the solution is an easy one too. Though I believe we have bigger problems.  
  
From what I read, the conversion from RGB to CMYK or the other way around isn't really about a formula. Some colors are not representable in one of the color spaces. ICC color profiles are required for correct transformation, and Adobe allows one to bundle them with a piece of software. I'm not sure about the exact license though.  
  
Also I believe that #407 doesn't have any connection to this.  
  
### Plan  
  
Just convert the type to `double` and close this issue. Then, attempt to adopt this library as an extension: [LIBICCPP](https://github.com/marco-o/LIBICCPP). It has a Boost license, but is unfinished.   
  
I believe properly adhering to color conversion rules would be a great boon for GIL.  
  
What do you think?  
  
/cc @mloskot @stefanseefeld

---

## Comment 2

> Username: mloskot  
> Created at: 2020-03-31 01:04:29 UTC  
> Url: https://github.com/boostorg/gil/issues/406#issuecomment-606334818  

@simmplecoder Yes, your analysis is valid and these issues were also pointed in the Wiki https://github.com/boostorg/gil/wiki/Boost.GIL-3-Ideas#extension-color-management and also with [suggestion for colorimetrically correct conversion](https://github.com/boostorg/gil/wiki/Boost.GIL-3-Ideas/_compare/11ecb8de692c73f2c8999eb0f931c26e6b3f431c). Such extension would be nice, and perhaps it's out there already :)  
  
  
However, `default_color_converter_impl<rgb_t, cmyk_t>` is an approximation and we rather should get it right as such. The problem is with `cmyk32_pixel_t` (or `cmyk64_pixel_t` if one goes crazy) due to the arithmetic resulting with values outside of valid ranges as @awulkiew spotted.  
  
In #470 I'm brainstorming a fix which seems fine, but it needs proper tests coverage for combinations of types, ranges and values. I will add tests later this week. Meanwhile, please have a look and review.

---

## Comment 3

> Username: mloskot  
> Created at: 2020-04-01 16:39:50 UTC  
> Updated at: 2020-04-01 18:53:56 UTC  
> Url: https://github.com/boostorg/gil/issues/406#issuecomment-607360166  

FYI, the fix I proposed needs to be corrected for signed integer channel values, see https://github.com/boostorg/gil/pull/470#issuecomment-607359925  
  
I'm also updating the description with collection of other incorrect conversion cases, as turns out CMYK32 is not the only broken case, see https://github.com/boostorg/gil/pull/470#issue-395993793
