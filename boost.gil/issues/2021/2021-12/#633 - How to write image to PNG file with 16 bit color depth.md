# #633 - How to write image to PNG file with 16 bit color depth? [Closed]

> Username: octopus-prime  
> Created at: 2021-12-24 11:25:00 UTC  
> Updated at: 2022-04-26 19:28:23 UTC  
> Closed at: 2022-04-26 19:28:23 UTC  
> Url: https://github.com/boostorg/gil/issues/633  

### Actual behavior  
  
Saving image as 8 bit -> ok  
![test](https://user-images.githubusercontent.com/7620013/147348822-72cbd228-040a-40d5-a744-4478e18fec16.png)  
  
Saving image as 16 bit -> broken  
![test16](https://user-images.githubusercontent.com/7620013/147348841-715d82c1-e74c-4789-abf1-cd848180fdfa.png)  
  
### Expected  behavior  
  
Saving image as 8 bit -> ok  
Saving image as 16 bit -> ok  
  
### C++ Minimal Working Example  
  
```cpp  
int main() {  
    gil::rgb32f_image_t image(1000, 1000);  
    auto view = gil::view(image);  
  
    rt::render(view, 10);  
  
    gil::write_view("test.png", gil::color_converted_view<gil::rgb8_pixel_t>(view), gil::png_tag());  
  
    gil::write_view("test16.png", gil::color_converted_view<gil::rgb16_pixel_t>(view), gil::png_tag());  
  
    return 0;  
}  
```  
  
### Environment  
  
boost 1.78.0  
libpng 1.6.37  
mingw w64 9.0 (bundled in clion windows)

---

## Comment 1

> Username: simmplecoder  
> Created at: 2021-12-25 10:41:39 UTC  
> Url: https://github.com/boostorg/gil/issues/633#issuecomment-1001002742  

Could you please provide original image and IHDR of the incorrect result?  (it would be great if the original file was uploaded to file exchange server, non-media website, because media processing might apply some transformations)  My expectation is that either IHDR is messed up in 16 bit path or some incorrect typing being applied.

---

## Comment 2

> Username: octopus-prime  
> Created at: 2021-12-25 13:27:30 UTC  
> Url: https://github.com/boostorg/gil/issues/633#issuecomment-1001018054  

Sowas?!  
  
`Format-Hex .\test16.png` (the broken one)  
  
->  
  
```  
00000000   89 50 4E 47 0D 0A 1A 0A 00 00 00 0D 49 48 44 52  PNG........IHDR  
00000010   00 00 03 E8 00 00 03 E8 10 02 00 00 00 92 51 9F  ...è...è.....Q¤  
00000030   65 D5 34 5C D7 E4 B4 39 E7 5D 36 91 11 90 20 22  eÕ4\×ä´9ç]6.`@Q.ET¨(A.Å  
00000050   08 A2 28 0A 2A 22 8A 01 C1 CC 03 12 24 2D 9B F3  .¢(.*".ÁÌ..$-  
00000060   EE 6C 98 9C 73 EA 9C A7 C3 4C F7 74 7D 75 5D DD  îlsê§ÃL÷t}u]Ý  
00000070   0B F8 7E DF F7 E7 AB FF CC DD BF BB FA DC 75 EA  .ø~ß÷ç«.ÌÝ¿»úÜuê  
00000080   D4 A9 63 30 FD 60 FA C1 F4 83 E9 07 D3 0F FE 33  Ô©c0ý`úÁôé.Ó.þ3  
```

---

## Comment 3

> Username: octopus-prime  
> Created at: 2021-12-25 13:29:38 UTC  
> Url: https://github.com/boostorg/gil/issues/633#issuecomment-1001018281  

And if you need the image... what about zip?

---

## Comment 4

> Username: simmplecoder  
> Created at: 2021-12-25 17:56:53 UTC  
> Url: https://github.com/boostorg/gil/issues/633#issuecomment-1001052965  

Yes, zip file would be great.

---

## Comment 5

> Username: simmplecoder  
> Created at: 2021-12-25 18:17:37 UTC  
> Url: https://github.com/boostorg/gil/issues/633#issuecomment-1001057081  

The IHDR looks correct. The bytes 03 E8 on the second row mean dimensions (1000, 1000), while bytes 10 and 02 mean 16 bit pixel depth and RGB/truecolor mode. So the problem is probably somewhere in the chunks.

---

## Comment 6

> Username: octopus-prime  
> Created at: 2021-12-25 18:47:38 UTC  
> Url: https://github.com/boostorg/gil/issues/633#issuecomment-1001060946  

Here the zip...  
[test16.zip](https://github.com/boostorg/gil/files/7776170/test16.zip)

---

## Comment 7

> Username: simmplecoder  
> Created at: 2021-12-26 19:08:10 UTC  
> Url: https://github.com/boostorg/gil/issues/633#issuecomment-1001228849  

@octopus-prime , I didn't check it completely yet, but perhaps you could have a look if there are any signed/unsigned problems in the program? I recalled that those patterns look a lot like overflows when converting from signed to unsigned. I will try to find which operation made it become like that before New Year.

---

## Comment 8

> Username: octopus-prime  
> Created at: 2022-01-30 12:49:09 UTC  
> Url: https://github.com/boostorg/gil/issues/633#issuecomment-1025136555  

This ist broken too:  
  
Ubuntu 21.10  
```  
-- Found Boost: /usr/lib/x86_64-linux-gnu/cmake/Boost-1.74.0/BoostConfig.cmake (found version "1.74.0")    
-- Found PNG: /usr/lib/x86_64-linux-gnu/libpng.so (found version "1.6.37")   
```  
  
```  
#include <boost/gil/image.hpp>  
#include <boost/gil/extension/io/png.hpp>  
  
namespace gil = boost::gil;  
  
int main() {  
    gil::rgb32f_image_t image(1000, 1000);  
    auto view = gil::view(image);  
  
    for (int y = 0; y < view.height(); ++y) {  
        for (int x = 0; x < view.width(); ++x) {  
            float value = std::abs(std::sin(x / 300.f) * std::cos(y / 200.f));  
            view (x, y) = gil::rgb32f_pixel_t(value, std::sqrt(value), std::cbrt(value));  
        }  
    }  
  
    gil::write_view("test8.png", gil::color_converted_view<gil::rgb8_pixel_t>(view), gil::png_tag());  
    gil::write_view("test16.png", gil::color_converted_view<gil::rgb16_pixel_t>(view), gil::png_tag());  
  
    return 0;  
}  
```  
  
8 bit  
![test8](https://user-images.githubusercontent.com/7620013/151700351-5b7b38ec-9965-481e-a188-28502bd5421e.png)  
  
16 bit  
![test16](https://user-images.githubusercontent.com/7620013/151700353-1193976e-d834-45ea-8c7e-0598648768e1.png)

---

## Comment 9

> Username: simmplecoder  
> Created at: 2022-01-30 17:03:52 UTC  
> Url: https://github.com/boostorg/gil/issues/633#issuecomment-1025185717  

I tried to do manual conversion, to exclude color converted view being the issue:  
  
```cpp      
    gil::rgb16_image_t img16(image.dimensions());  
    auto view16 = gil::view(img16);  
    gil::transform_pixels(view, view16, [](const gil::rgb32f_pixel_t& src) {  
        gil::rgb16_pixel_t result;  
        auto coefficient = std::numeric_limits<std::uint16_t>::max();  
        result[0] = static_cast<unsigned short>(src[0] * coefficient);  
        result[1] = static_cast<unsigned short>(src[1] * coefficient);  
        result[2] = static_cast<unsigned short>(src[2] * coefficient);  
  
        return result;  
    });  
  
    gil::write_view("test16.png", view16, gil::png_tag());  
```  
  
The image was still broken. The problem is definitely somewhere in the IO PNG extension. I looked in [write.hpp](https://github.com/boostorg/gil/blob/develop/include/boost/gil/extension/io/png/detail/write.hpp), but nothing stood out as erroneous (libpng seems to have deeply nested macros).   
  
Is there any specific place I should look at? @chhenning @mloskot @lpranam

---

## Comment 10

> Username: striezel  
> Created at: 2022-04-25 18:12:02 UTC  
> Url: https://github.com/boostorg/gil/issues/633#issuecomment-1108885397  

> This ist broken too:  
>   
> Ubuntu 21.10  
>   
> ```  
> -- Found Boost: /usr/lib/x86_64-linux-gnu/cmake/Boost-1.74.0/BoostConfig.cmake (found version "1.74.0")    
> -- Found PNG: /usr/lib/x86_64-linux-gnu/libpng.so (found version "1.6.37")   
> ```  
>   
> ```  
> #include <boost/gil/image.hpp>  
> #include <boost/gil/extension/io/png.hpp>  
>   
> namespace gil = boost::gil;  
>   
> int main() {  
>     gil::rgb32f_image_t image(1000, 1000);  
>     auto view = gil::view(image);  
>   
>     for (int y = 0; y < view.height(); ++y) {  
>         for (int x = 0; x < view.width(); ++x) {  
>             float value = std::abs(std::sin(x / 300.f) * std::cos(y / 200.f));  
>             view (x, y) = gil::rgb32f_pixel_t(value, std::sqrt(value), std::cbrt(value));  
>         }  
>     }  
>   
>     gil::write_view("test8.png", gil::color_converted_view<gil::rgb8_pixel_t>(view), gil::png_tag());  
>     gil::write_view("test16.png", gil::color_converted_view<gil::rgb16_pixel_t>(view), gil::png_tag());  
>   
>     return 0;  
> }  
> ```  
>   
> 8 bit ![test8](https://user-images.githubusercontent.com/7620013/151700351-5b7b38ec-9965-481e-a188-28502bd5421e.png)  
>   
> 16 bit ![test16](https://user-images.githubusercontent.com/7620013/151700353-1193976e-d834-45ea-8c7e-0598648768e1.png)  
  
For what it is worth:  
I've compiled your example with Boost 1.71, libpng 1.6.37 and GNU GCC 9.4.0 on Ubuntu 20.04 (LTS) and the 16 bit image comes out the way it should, looking like the 8 bit version.  
  
![test16](https://user-images.githubusercontent.com/20865852/165148386-b8ab4533-1c72-4038-872c-63523b663a54.png)  
  
So it may be either a compiler issue, or - more likely - some bug introduced between Boost 1.71 and Boost 1.74. I hope that helps to narrow things down a bit.

---

## Comment 11

> Username: striezel  
> Created at: 2022-04-26 00:38:54 UTC  
> Url: https://github.com/boostorg/gil/issues/633#issuecomment-1109174009  

@octopus-prime:  
Could you please test the example from above (https://github.com/boostorg/gil/issues/633#issuecomment-1025136555) with the changes from the pull request https://github.com/boostorg/gil/pull/650? For me that fixes the problem. However, to be sure I would like  you to verify that.

---

## Comment 12

> Username: octopus-prime  
> Created at: 2022-04-26 15:06:39 UTC  
> Url: https://github.com/boostorg/gil/issues/633#issuecomment-1109913629  

@striezel   
Works like a charm! 👍   
Thanks!

---

## Comment 13

> Username: mloskot  
> Created at: 2022-04-26 15:27:52 UTC  
> Url: https://github.com/boostorg/gil/issues/633#issuecomment-1109939034  

Awesome, thank you both!   
I'll merge the fixing PR tonight.

---

## Comment 14

> Username: mloskot  
> Created at: 2022-04-26 19:28:23 UTC  
> Url: https://github.com/boostorg/gil/issues/633#issuecomment-1110169070  

I've merged the PR #650 with the fix. Thanks again!
