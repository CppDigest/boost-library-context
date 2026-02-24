# #223 - Writing a gray16 PNG image on disk always results on an empty (all 0) image. [Open]

> Username: renatoGarcia  
> Created at: 2019-01-24 19:01:01 UTC  
> Updated at: 2020-04-06 20:03:30 UTC  
> Url: https://github.com/boostorg/gil/issues/223  

When writing a gray16 PNG image, none error will arise at compile time or runtime, and the resulting image on disk will have all pixels with a value of '0'.  
  
The following code reproduces the bug:  
  
    #include <boost/gil/extension/io/png.hpp>  
    #include <boost/gil/image.hpp>  
    #include <cstdint>  
  
    namespace bg = boost::gil;  
  
    int main(int argc, char *argv[])  
    {  
        auto img = bg::gray16_image_t {10, 20, bg::gray16_pixel_t {0}};  
        auto view = bg::view(img);  
        auto count = std::uint16_t {0};  
        for (auto it = view.begin(); it != view.end(); ++it)  
        {  
            *it = count++;  
        }  
  
        bg::write_view("img.png", bg::const_view(img), bg::png_tag());  
        return 0;  
    }  
  
When changing the image/view types to gray8 on code above, a correct image is wrote.  
  
The used system is an up to date ArchLinux with gcc 8.2.1, boost 1.69, libpng 1.6.36.

---

## Comment 1

> Username: codejaeger  
> Created at: 2020-03-03 19:00:48 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-594112897  

@mloskot This issue seems to have been resolved, gray16 works as expected in the example provided.

---

## Comment 2

> Username: mloskot  
> Created at: 2020-03-03 19:04:31 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-594114717  

@codejaeger Although manual verification using an example is good, it is not good enough. We need to add proper test for this particular case, then this issue can be closed.

---

## Comment 3

> Username: codejaeger  
> Created at: 2020-03-03 19:28:30 UTC  
> Updated at: 2020-03-03 19:49:57 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-594125717  

Yes, sorry I shouldn't have commented in such a haste .   
@mloskot what kind of tests would you propose for these ? What I mean is that , I went through gil's test suite and discovered a png_test.cpp, which seemed to be somewhat checking write_view for png . I am going through the gil design guides simultaneously , and could add some proper test cases if needed.

---

## Comment 4

> Username: mloskot  
> Created at: 2020-03-03 19:55:06 UTC  
> Updated at: 2020-03-03 19:55:36 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-594138169  

@codejaeger  
  
No problem.  
  
> what kind of tests would you propose for these ?  
  
As @renatoGarcia explains the undesired behaviour is:  
  
> writing a gray16 PNG image results in image  with all pixels having value of `0`  
  
a test should verify that all pixels of such written to file have correct values.  
  
I'd add a new test case to `test/extension/io/png_test.cpp`

---

## Comment 5

> Username: codejaeger  
> Created at: 2020-03-03 20:04:20 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-594142156  

> I'd add a new test case to `test/extension/io/png_test.cpp`  
  
Thanks, will do it.

---

## Comment 6

> Username: codejaeger  
> Created at: 2020-03-04 17:04:08 UTC  
> Updated at: 2020-03-04 17:06:23 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-594664605  

@mloskot Although the above example works correctly, I am getting an unexpected behavior when reading back a gray16 image from the disk. The below example should reproduce the issue.   
```  
#include <boost/gil.hpp>  
#include <string.h>  
#include <boost/gil/extension/io/png.hpp>  
#include <boost/core/lightweight_test.hpp>  
using namespace boost::gil;  
int main()  
{  
    point_t dim(10, 10);  
    gray16_image_t expected(dim,gray16_pixel_t(1));  
    write_view("./out-1.png", view(expected), png_tag{});  
    std::string filename( "./out-1.png" );  
    gray16_image_t img;  
    read_image( filename, img, png_tag{} );  
    BOOST_TEST(boost::gil::equal_pixels(view(expected), view(img)));  
    return boost::report_errors();  
}  
```  
I traced the error and it seems on reading back from disk or writing to the disk , pixels values get left shifted by 8 bits , which causes the test to fail. Is this an expected behaviour?  
And as mentioned in the first post , it works for gray8 but fails for gray16.

---

## Comment 7

> Username: mloskot  
> Created at: 2020-03-04 17:32:01 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-594686219  

@codejaeger Can you try the same for JPEG and/or TIFF and compare? We need to identify if the issue occurs at I/O level or in the core.

---

## Comment 8

> Username: codejaeger  
> Created at: 2020-03-04 18:03:57 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-594712561  

I ran for all possible combinations on the above example:  
JPEG - gray16,rgb16 fails to compile, others pass  
PNG - gray16,rgb16 do not pass, others pass  
TIFF - all pass  
I am trying to understand why jpeg fails to compile on the above example. Does the above give a clue to what is happening? Maybe in the I/O level?

---

## Comment 9

> Username: mloskot  
> Created at: 2020-03-04 18:18:52 UTC  
> Updated at: 2020-03-04 18:19:00 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-594723627  

> JPEG - gray16,rgb16 fails to compile, others pass  
  
Since support for 16 bpp is dependant on codec library, we may not allow/support it. Simple as that.  
  
For libjpeg itself, see `Subject: [21] What if I need more than 8-bit precision?` at http://www.faqs.org/faqs/jpeg-faq/part1/  
  
I'm not sure if libjpeg-turbo has received the 16 bpp support as discussed in [[Libjpeg-turbo-devel] 8 or 16 bits RGB input](https://sourceforge.net/p/libjpeg-turbo/mailman/message/30101527/).  
  
I hardly ever use 16 bpp myself.  
  
I suggest to stick to TIFF as reference implementation for this issue.

---

## Comment 10

> Username: codejaeger  
> Created at: 2020-03-04 18:50:27 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-594746854  

> I suggest to stick to TIFF as reference implementation for this issue.  
  
@mloskot If I guess correctly , I think this would address the issue above , but while reading up png's test suite in this pursuit  
  
> I'd add a new test case to `test/extension/io/png_test.cpp`  
  
, I couldn't find tests that check for cases like above , i.e. image written to the disk has correct pixels or not . Since gray16 does not seem to work properly , should I submit a PR adding tests for gray8,rgb8 in png's test suite. I believe such tests verify a fairly basic assumption about what the function read_image or write_image intend to do. Although it is not necessary at all , I am just following this advice : )  
  
> @codejaeger Although manual verification using an example is good, it is not good enough.   
  
Please correct me if I am wrong.

---

## Comment 11

> Username: mloskot  
> Created at: 2020-03-04 19:24:21 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-594773240  

To summarise what we know now about the issue of 16-bit grayscale image I/O:  
  
1. GIL write/read of TIFF works  
2. GIL write of PNG and read of PNG fails (pixel data is damaged)  
  
So, we can focus for now on PNG only and I'd focus on confirming basics:  
  
**Can GIL read a file with 16-bit grayscale image?** I mean, a reference file from independent sources, not a file written by GIL.  
  
We have lots of PNG read/write tests, some do exercise 16-bit images, for example  
  
https://github.com/boostorg/gil/blob/808eafbd6cd903814b36622884cf7649c8ff0f8d/test/extension/io/png_read_test.cpp#L128-L134  
  
https://github.com/boostorg/gil/blob/808eafbd6cd903814b36622884cf7649c8ff0f8d/test/extension/io/png_read_test.cpp#L377-L379  
  
but the trick (due to still a mess of those tests) is that, you need to compile with   
  
- `-DBOOST_GIL_IO_TEST_ALLOW_READING_IMAGES=1`  
- `-DBOOST_GIL_IO_USE_PNG_TEST_SUITE_IMAGES=1`  
  
and download the reference PNG images from http://www.schaik.com/pngsuite/pngsuite.html  
and put them in [test/extension/io/images/png/PngSuite](https://github.com/boostorg/gil/tree/808eafbd6cd903814b36622884cf7649c8ff0f8d/test/extension/io/images/png/PngSuite), where we already have some, but not all. So, a bit of preparation is necessary here.  
Instead of messing with the GIL test suite, you can replicate the test cases in minimal example program you can run and debug easier.  
  
If we can read a reference image, then GIL reading of PNG is sound.  
If we can not read it, then GIL reading of PNG is broken...

---

## Comment 12

> Username: codejaeger  
> Created at: 2020-03-05 12:26:06 UTC  
> Updated at: 2020-03-31 17:42:45 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-595203602  

@mloskot   
So, I tried debugging as you had instructed; these are my observations  
Read from a image generated from external source(matlab imwrite)   
([al_255.png](https://user-images.githubusercontent.com/32168969/75980183-fb13a580-5f07-11ea-8acf-7cfc367e0c1c.png)  
 , it is a 20x20 white(all pixels(16 bits) have value 255)  
Example used :-  
```  
#include <boost/gil/extension/io/png.hpp>  
#include <boost/core/lightweight_test.hpp>  
#include <string.h>  
using namespace boost::gil;  
int main()  
{  
    point_t in(20, 20);  
    gray16_image_t expected(in,gray16_pixel_t(255));  
    std::string filename( "./al_255.png" );     
    gray16_image_t img;  
    read_image( filename, img, png_tag{} );  
    BOOST_TEST(boost::gil::equal_pixels(view(expected), view(img)));  
    return boost::report_errors();  
}  
```  
Output - No error detected.  
Expected - No errors detected.  
  
I took the liberty to check write_view for gray16 using the following example:-  
```  
#include <boost/gil/extension/io/png.hpp>  
#include <boost/core/lightweight_test.hpp>  
using namespace boost::gil;  
int main()  
{  
    point_t in(20, 20);  
    gray16_image_t expected(in,gray16_pixel_t(255));  
    write_view("./al_255.png", view(expected), png_tag{});  
    return boost::report_errors();  
}  
```  
And read the following image using a third party (Matlab's imread) software, and all pixels were 0 . It looks write of 16 bits png image views is broken. Please correct me if I am wrong.   
**And one more thing** , the file png_read_test.cpp does tests on a bunch of images. Although I didn't get the time to do this   
  
> and download the reference PNG images from http://www.schaik.com/pngsuite/pngsuite.html  
  
(Maybe I will write a script to download the needed files and run the tests later)  
I discovered that the tests in the file png_read_test.cpp make the assumption that at least one of read_image or write_view functions works correctly for all image types(16 bit or 8 bit,rgb or gray etc.). I think this leads to a cyclic dependency of tests, and maybe we also ought to do a read_test properly in each of png's, jpeg's and tiff's test suites separately. This will help debugging in future.

---

## Comment 13

> Username: mloskot  
> Created at: 2020-03-07 20:54:15 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-596136564  

@codejaeger Quick note, I really appreciate your efforts and please be assured I will respond as soon as I have a moment to check the issue in details.

---

## Comment 14

> Username: codejaeger  
> Created at: 2020-03-08 04:42:10 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-596166144  

Yeah, no problem @mloskot . Anyways I would be a little busy these days preparing my GSoC proposal and delaying working on this issue is better for me. Thanks.

---

## Comment 15

> Username: mloskot  
> Created at: 2020-03-27 14:09:41 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-605020918  

@renatoGarcia   
> The used system is an up to date ArchLinux with gcc 8.2.1, boost 1.69, libpng 1.6.36.  
  
Is it possible for you to try the current `develop` branch?

---

## Comment 16

> Username: renatoGarcia  
> Created at: 2020-04-05 17:21:29 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-609451185  

@mloskot   
At 4325be7 (boost-1.72.0) both [my code](https://github.com/boostorg/gil/issues/223#issue-402850795) (manual image checking) and [@codejaeger one](https://github.com/boostorg/gil/issues/223#issuecomment-594664605) was working fine. At c887d3b (develop head) my test works fine, but the one of codejaeger test with error. I have `git bisect` it to this commit: 5f3c002

---

## Comment 17

> Username: mloskot  
> Created at: 2020-04-05 17:25:36 UTC  
> Updated at: 2020-04-05 17:26:05 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-609451877  

@renatoGarcia  
I'd argue the fix in 5f3c00244bdc8128d53fa569d17a12a8b80642db is correct and if it breaks anything, then that thing needs fixing.

---

## Comment 18

> Username: renatoGarcia  
> Created at: 2020-04-05 18:25:12 UTC  
> Updated at: 2020-04-05 18:25:34 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-609460417  

@mloskot  
Oh man, sure! You are right!  
  
The code at [here](https://github.com/boostorg/gil/issues/223#issuecomment-594664605) when calling `gray16_image_t expected(dim,gray16_pixel_t(1))`, before 5f3c002 was actually calling [this](https://github.com/boostorg/gil/blob/5f3c00244bdc8128d53fa569d17a12a8b80642db/include/boost/gil/image.hpp#L64) constructor.  
  
As no constructor with signature `image(const point_t&, const Pixel&)` existed, the `gray16_pixel_t(1)` was coerced to `std::size_t`. When calling the same code with `gray16_image_t expected (dim, gray16_pixel_t(1), 0)` and hence calling the same constructor as after 5f3c002 the same error arises.  
  
Anyway. Writing images [as in](https://github.com/boostorg/gil/issues/223#issue-402850795) is working fine on develop. And the code at [here](https://github.com/boostorg/gil/issues/223#issuecomment-594664605) fails.

---

## Comment 19

> Username: renatoGarcia  
> Created at: 2020-04-05 23:40:29 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-609504455  

IMO, although there are another question, the bug reported on this issue was solved. Should I close it?

---

## Comment 20

> Username: mloskot  
> Created at: 2020-04-06 20:03:30 UTC  
> Url: https://github.com/boostorg/gil/issues/223#issuecomment-610007868  

TBH, I'm a bit lost about what is fixed what is not, what MWE is correct and what not, but I don't have time now to check myself, so let's keep it open for now.
