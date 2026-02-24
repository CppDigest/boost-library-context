# #727 - Support for reading/writing 1 bit (black/white) BMP images? [Open]

> Username: dynarithmic  
> Created at: 2023-02-11 23:43:26 UTC  
> Updated at: 2024-05-05 19:12:10 UTC  
> Url: https://github.com/boostorg/gil/issues/727  

### Actual behavior  
  
Attempting to read the attached monochrome BMP image using gil will result in an "Image types aren't compatible" exception being thrown from io_error() in error.hpp.  
  
### Expected  behavior  
Valid load of a valid 1-bit BMP image on the gil::read_image() call;  
  
### C++ Minimal Working Example  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
```cpp  
#include <boost/gil.hpp>  
#include <boost/gil/extension/io/bmp.hpp>  
#include <fstream>  
namespace gil = boost::gil;  
  
int main()  
{  
    std::ifstream infile("test1bpp.bmp", std::ios::binary);  
    gil::rgb8_image_t img;  // <-- This line probably is not correct, but don't know what this should be  
    gil::read_image(infile, img, gil::bmp_tag());  
}  
```  
I am new to gil, and more than likely, the img type is not correct, which is not surprising.  However I was not able to use any of the other types that are predefined without compilation error from the template compile checks, or if the compilation succeeds, the same exception being thrown at runtime ("Image types not compatible").  
  
So I am not sure if 1-bpp images are supported in gil, and/or what should be done in the existing gil library to read the attached image.  
  
### Environment  
- Compiler version: Visual Studio 2019  
- Build settings: Debug / Release / 32-bit  
- Version (Git ref or `<boost/version.hpp>`): 1.81  
[test1bpp.zip](https://github.com/boostorg/gil/files/10714953/test1bpp.zip)

---

## Comment 1

> Username: dynarithmic  
> Created at: 2023-02-12 02:29:24 UTC  
> Url: https://github.com/boostorg/gil/issues/727#issuecomment-1426925293  

I was able to get a little further using the following  
  
```cpp  
#include <boost/gil.hpp>  
#include <boost/gil/extension/io/bmp.hpp>  
#include <fstream>  
namespace gil = boost::gil;  
  
int main()  
{  
    std::ifstream infile("test1bpp.bmp", std::ios::binary);  
    gil::rgba8_image_t img;  
    gil::read_image(infile, img, gil::bmp_tag()); // <-- Doesn't throw exception now  
  
    // Now write the image to another bmp file  
    auto v = gil::view(img);  
    auto writer = gil::make_writer("test1bpp_output.bmp", gil::bmp_tag());  
    gil::write_view(writer, v);  // <-- This creates a 24 bpp image, not a 1 bpp image  
}  
```  
However, the code to write the image to a second bmp file results in the file being 24 bits-per-pixel, and not the original 1-bit-per-pixel as was the source image.   I debugged the code, and it seems that the write_view multiplies the number of channels by 8.  I am not sure this will work for 1-bit-per-pixel.  
  
So I am stuck as to how to write the destination bmp using the same 1-bpp as the source image.  
  
The ultimate goal is to be able to convert a 1-bpp bmp to a Group3 or Group4 TIFF, which only supports 1-bit-per-pixel image data.   So far, I am able to read the data, but not able to produce a duplicate of the image, even if the image type is the same, source and destination.

---

## Comment 2

> Username: striezel  
> Created at: 2024-05-05 19:10:27 UTC  
> Updated at: 2024-05-05 19:12:10 UTC  
> Url: https://github.com/boostorg/gil/issues/727#issuecomment-2094916601  

> I was able to get a little further using the following  
>   
> ```c++  
> #include <boost/gil.hpp>  
> #include <boost/gil/extension/io/bmp.hpp>  
> #include <fstream>  
> namespace gil = boost::gil;  
>   
> int main()  
> {  
>     std::ifstream infile("test1bpp.bmp", std::ios::binary);  
>     gil::rgba8_image_t img;  
>     gil::read_image(infile, img, gil::bmp_tag()); // <-- Doesn't throw exception now  
>     ...  
> ```  
@dynarithmic:  
I am actually surprised that this works, because from my understanding it should not. :thinking: It probably works, because the image is 1700 x 2000 pixels, and that multiplied by one bit for each pixel gives a number that is divisible by 32 but not by 24. Have you checked the data? It may not be what you expect.  
  
But let's get back to the first example using `gil::rgb8_image_t`. You can actually make that work by using `read_and_convert_image()` instead of `read_image()`:  
```c++  
#include <boost/gil.hpp>  
#include <boost/gil/extension/io/bmp.hpp>  
#include <fstream>  
  
namespace gil = boost::gil;  
  
int main()  
{  
    std::ifstream infile("test1bpp.bmp", std::ios::binary);  
    gil::rgb8_image_t img;  
    gil::read_and_convert_image(infile, img, gil::bmp_tag());  
}  
```  
The advantage of `read_and_convert_image()` over `read_image()` is that it converts the read image data (in that case: 1 bpp grayscale) to the given image type (in that case: 24 bpp RGB), if GIL can do that, thus potentially avoiding problems with wrong image types.  
  
As far as writing BMP images is concerned, GIL currently only supports writing of 24 bit RGB and 32 bit RGBA bitmaps. At least that is how I interpret that code: https://github.com/boostorg/gil/blob/8994c2ff8ed8b88e0ffef9658a4c603c4a001fad/include/boost/gil/extension/io/bmp/detail/supported_types.hpp#L88-L106
