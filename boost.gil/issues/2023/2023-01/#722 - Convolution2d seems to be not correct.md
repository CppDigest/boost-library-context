# #722 - Convolution2d seems to be not correct. [Closed]

> Username: cgringmuth  
> Created at: 2023-01-31 11:34:20 UTC  
> Updated at: 2023-02-07 14:03:36 UTC  
> Closed at: 2023-02-07 13:10:41 UTC  
> Url: https://github.com/boostorg/gil/issues/722  

### Actual behavior  
  
If i provide the example `sobel_scharr.cpp` with following image: ![small_box2](https://user-images.githubusercontent.com/1169481/215752715-26d3591f-b373-43d8-a024-5a064ad44841.png)  
I get (for sobel filter)   
|Grad X| Grad Y|  
|--------| -------|  
| ![output2-x](https://user-images.githubusercontent.com/1169481/215752796-6d60e68f-eb5f-42ad-b852-211e3ed80161.png)| ![output2-y](https://user-images.githubusercontent.com/1169481/215752924-df9fbf66-3978-4d84-be7c-e6acb698e8d9.png) |  
  
  
Which is not correct in my opinion.  
  
### Expected  behavior  
I have written a unit test which explains what I would expect from a 2 d convolution with sobel filter for the x direction.  
  
```cpp  
void test_convolve_2d_with_sobel_x_filter()  
{  
  const gil::uint8_t img[] =  
      {  
          0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
          0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
          0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
          0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
          0, 0, 255, 255, 255, 255, 0, 0, 0, 0,  
          0, 0, 255, 255, 255, 255, 0, 0, 0, 0,  
          0, 0, 255, 255, 255, 255, 0, 0, 0, 0,  
          0, 0, 255, 255, 255, 255, 0, 0, 0, 0,  
          0, 0, 255, 255, 255, 255, 0, 0, 0, 0,  
          0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
          0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
          0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
          0, 0, 0, 0, 0, 0, 0, 0, 0, 0  
      };  
  
  const gil::int16_t exp_output[] =  
      {  
          0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
          0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
          0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
          0, 255, 255, 0, 0, -255, -255, 0, 0, 0,  
          0, 765, 765, 0, 0, -765, -765, 0, 0, 0,  
          0, 1020, 1020, 0, 0, -1020, -1020, 0, 0, 0,  
          0, 1020, 1020, 0, 0, -1020, -1020, 0, 0, 0,  
          0, 1020, 1020, 0, 0, -1020, -1020, 0, 0, 0,  
          0, 765, 765, 0, 0, -765, -765, 0, 0, 0,  
          0, 255, 255, 0, 0, -255, -255, 0, 0, 0,  
          0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
          0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
          0, 0, 0, 0, 0, 0, 0, 0, 0, 0  
      };  
  
  gil::gray16s_image_t img_gray_out(10, 13);  
  auto src_view =  
      gil::interleaved_view(10, 13, reinterpret_cast<const gil::gray8_pixel_t*>(img), 10);  
  auto exp_out_view =  
      gil::interleaved_view(10, 13, reinterpret_cast<const gil::gray16s_pixel_t*>(exp_output), 20);  
  gil::detail::convolve_2d(src_view, gil::generate_dx_sobel(1), view(img_gray_out));  
    
  BOOST_TEST(gil::equal_pixels(exp_out_view, view(img_gray_out)));  
}  
```  
  
### C++ Minimal Working Example  
See unit test in Expected  behavior.  
  
### Environment  
Should be independent of environment. I didn't have any crash or something.  
  
  
PS: I also don't know how to properly debug code in GIL properly. How I can see the content of an image in the debugger?

---

## Comment 1

> Username: cgringmuth  
> Created at: 2023-01-31 12:32:01 UTC  
> Updated at: 2023-02-07 14:03:36 UTC  
> Url: https://github.com/boostorg/gil/issues/722#issuecomment-1410265942  

I think I fixed the issue already. Will create PR.
