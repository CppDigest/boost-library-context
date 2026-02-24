# #170 - read_image bmp 8 bit returns "Image types aren't compatible" [Closed]

> Username: Nelson-numerical-software  
> Created at: 2018-11-25 19:55:23 UTC  
> Updated at: 2025-12-10 07:11:17 UTC  
> Closed at: 2025-12-10 07:11:17 UTC  
> Url: https://github.com/boostorg/gil/issues/170  

version: boost 1.68  
  
It is possible to read easily a 24 bit BMP with:  
```  
rgb8_image_t  runtime_image;  
read_image(filename, runtime_image, boost::gil::bmp_tag());  
```  
  
With 8 bit bmp (gray color) image, it  fails with this message "Image types aren't compatible"  
[lena_gray.zip](https://github.com/boostorg/gil/files/2613435/lena_gray.zip)  
  
I tried:  
```  
    typedef boost::mpl::vector<gray1_image_t, gray2_image_t, gray4_image_t, gray6_image_t,  
        gray10_image_t, gray12_image_t, gray14_image_t, gray24_image_t,  
        gray8_image_t, rgb8_image_t, bgr8_image_t, rgb64f_image_t, rgb64f_planar_image_t,  
        rgba64f_image_t, rgba64f_planar_image_t, gray64f_image_t>  
        my_img_types;  
    any_image<my_img_types> runtime_image;  
  
    read_image(filename, runtime_image, boost::gil::bmp_tag());  
```  
Please provide an easy working example  
(sorry, I did not find a easy tutorial about this)

---

## Comment 1

> Username: mloskot  
> Created at: 2018-11-27 11:57:42 UTC  
> Updated at: 2018-11-27 11:58:04 UTC  
> Url: https://github.com/boostorg/gil/issues/170#issuecomment-442033133  

Try the suggestion from https://www.boost.org/doc/libs/1_68_0/libs/gil/doc/html/io.html  
> The `read_image()` expects the supplied image type to be compatible with the image stored in the file.  
> If the user doesn’t know what format an image has she can use `read_and_convert_image()`.   
  
----  
@Nelson-numerical-software   
> Please provide an easy working example (sorry, I did not find a easy tutorial about this)  
  
GIL is a complex library. GIL documentation needs improvements and we try to improve it with every release, but given the limited resources of the modest team, it is slow process.
