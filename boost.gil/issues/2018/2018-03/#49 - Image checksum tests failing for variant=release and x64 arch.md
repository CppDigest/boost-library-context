# #49 - Image checksum tests failing for variant=release and x64 arch [Open]

> Username: mloskot  
> Created at: 2018-03-16 21:03:55 UTC  
> Updated at: 2018-10-23 17:11:50 UTC  
> Url: https://github.com/boostorg/gil/issues/49  

Adding `b2 variant=release` build jobs to AppVeyor in #46 which revealed that `test/image.cpp` is failing on **optimised x64 only** builds:  
  
```  
Checksum error in bgr121_basic_red_x (crc=2009802095 != 2137924839)  
```  
  
Here is the build https://ci.appveyor.com/project/stefanseefeld/gil/build/1.0.182-develop  
  
This issue does not occur on Travis using GCC 5.4.1 and clang 4.2.1.  
I also was not able to reproduce it locally, on Ubuntu with GCC 5.4.0.  
  
## Failing checksum test cases  
  
Modifying `test/image.cpp` to skip cases with missing CRC-s:  
  
```  
 // Create a checksum for the given view and compare it with the reference checksum. Throw exception if different  
 void checksum_image_test::check_view_impl(const rgb8c_view_t& img_view, const string& name) {  
+    if (_crc_map[name] == 0)  
+    {  
+        cerr << "no checksum, skipping " << name << endl;  
+        return;  
+    }  
+  
     boost::crc_32_type checksum_acumulator;  
     checksum_acumulator.process_bytes(img_view.row_begin(0),img_view.size()*3);  
```  
  
allowed to iterate over the test cases in `test/gil_reference_checksums.txt` (remove failing entry, run the test, repeat until all remaining cases pass) and identify the failing ones:  
  
```  
bgr121_basic_red_x 7f6e24e7  
bgr121_basic_white_x e4aaa1d3  
bgr121_views_90cw 8565efd8  
bgr121_views_flipped_ud 8f7f7d00  
```

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-03-18 20:51:00 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-374045635  

So if I understand correctly,  the `boost::crc_32_type.process_bytes()` function above computes ("accumulates") different numbers depending on whether you compile a `release` or `debug` variant.  
That sounds like a bug in either the checksum code (relying on undefined behaviour) or the compiler (producing incorrect code during optimization). Could you try to reduce the test case, i.e. make it independent of Boost.GIL, and then submit it to the Boost.CRC library ?  
I'd even argue that we should (temporarily) disable those checks, once we know the bug isn't ours.

---

## Comment 2

> Username: mloskot  
> Created at: 2018-03-18 21:13:27 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-374047695  

I'm also suspicious about the `crc_32_type` leaving slight chance for misbehaving `img_view` (or compiler tripping over it) here  
  
```  
boost::crc_32_type checksum_acumulator;  
checksum_acumulator.process_bytes(img_view.row_begin(0),img_view.size()*3);  
```  
  
I'll try to reduce the case during the week.

---

## Comment 3

> Username: mloskot  
> Created at: 2018-03-22 00:23:05 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-375138543  

I've changed the `test/image.cpp` to **temporarily** skip the four failing test case.

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2018-03-22 00:27:51 UTC  
> Updated at: 2018-03-22 00:28:10 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-375139296  

How does the checksum accumulator work ? I assume it's iterating over some sequence (of bytes ?). So it ought to be possible to pre-process the data, such that the input to the `process_bytes` function doesn't have to be an image, but raw data, thereby removing the test's dependency on Boost.GIL.  
That would result in a "minimal" test case (one only depending on Boost.CRC) that (presumably, hopefully) still fails when compiled in "release" mode. At that point we could hand it over to the Boost.CRC issue tracker and let them deal with the issue.

---

## Comment 5

> Username: mloskot  
> Created at: 2018-03-22 17:06:58 UTC  
> Updated at: 2018-06-23 10:34:53 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-375383391  

### Test 1  
  
![image-crc-bug](https://user-images.githubusercontent.com/80741/37784392-025f0926-2df8-11e8-9e4b-972e214e301e.png)  
  
`image_crc_minimal` runs series of test cases with `NxN` images where `N = {2, 3, 4, ..., N+1}` each time performs 3 steps:  
  - create empty `NxN` image  
  - fill image with red  
     - calculate CRC  
  - draw blue line diagonal  
     - calculate CRC  
  
###  Bug  
  
For **VS2017 64-bit optimised build**, the *blue line* step seems to incorrectly calculate location of pixels to fill or damage the image in memory (see screenshot below).  
  
### MWE  
  
Source of the program that runs this minimal 'fuzz' testing above:  
  
```  
D:\dev\boost-gil-workshop\crc (image-crc-bug-test)  
λ cat image_crc_minimal.cpp  
#define BOOST_CONFIG_SUPPRESS_OUTDATED_MESSAGE  
#ifdef _MSC_VER  
#pragma warning(disable:4244)  
#pragma warning(disable:4503)  
#pragma warning(disable:4714)  
#pragma warning(disable:4996)  
#endif  
#include <filesystem>  
#include <fstream>  
#include <iostream>  
#include <string>  
#include <vector>  
#include <boost/algorithm/hex.hpp>  
#include <boost/crc.hpp>  
#include <boost/gil/gil_all.hpp>  
#include <boost/gil/extension/dynamic_image/dynamic_image_all.hpp>  
#include <boost/mpl/vector.hpp>  
using namespace std;  
using namespace boost::gil;  
namespace fs = std::experimental::filesystem;  
  
struct image_test  
{  
    std::ostream& log_;  
    image_test(std::ostream& os) : log_(os) {}  
  
    void print_checksum(const rgb8c_view_t& img_view, const string& name)  
    {  
        boost::crc_32_type checksum_acumulator;  
        checksum_acumulator.process_bytes(img_view.row_begin(0), img_view.size() * 3);  
        cerr << "Checksum (" << name << "):\t\t" << std::hex << checksum_acumulator.checksum() << endl;  
        log_ << "Checksum (" << name << "):\t\t" << std::hex << checksum_acumulator.checksum() << endl;  
    }  
  
    void print_view_hex(const rgb8c_view_t& img_view)  
    {  
        auto const raw_size = img_view.size() * 3;  
        unsigned char const* raw_ptr = &img_view[0][0];  
        std::vector<unsigned char> raw(raw_ptr, raw_ptr + raw_size);  
        std::string out(raw_size * 2, char{0});  
        boost::algorithm::hex(raw, out.begin());  
        log_ << out << endl;  
    }  
  
    template <typename View>  
    void check_view(const View& img_view, const string& name)  
    {  
        rgb8_image_t rgb_img(img_view.dimensions());  
        copy_and_convert_pixels(img_view, view(rgb_img));  
        print_checksum(const_view(rgb_img), name);  
        print_view_hex(const_view(rgb_img));  
    }  
  
    template <typename Img>  
    void basic_test(typename Img::point_t const& dims)  
    {  
        using view_value_t = typename Img::view_t::value_type;  
  
        // make empty image  
        Img img(dims);  
        auto const& img_view = view(img);  
        //check_view(img_view, "make"); // possibly random garbage  
  
        // colors  
        rgb8_pixel_t red8(255, 0, 0), green8(0, 255, 0), blue8(0, 0, 255), white8(255, 255, 255);  
        view_value_t red, green, blue, white;  
        color_convert(red8, red);  
        color_convert(green8, green);  
        color_convert(blue8, blue);  
        color_convert(white8, white);  
  
        // fill it with red  
        fill(img_view.begin(), img_view.end(), red);  
        check_view(img_view, "red  fill");  
  
        // draw a blue line along the diagonal  
        {  
            auto loc = img_view.xy_at(0, img_view.height() - 1);  
            for (int y = 0; y < img_view.height(); ++y)  
            {  
                *loc = blue;  
                ++loc.x();  
                loc.y()--;  
            }  
            check_view(img_view, "blue line");  
        }  
    }  
};  
  
#ifdef NDEBUG  
auto const config_suffix = "opt";  
#else  
auto const config_suffix = "dbg";  
#endif  
#if defined(_WIN64) || defined(__x86_64__) || defined(__LLP64__) || (UINTPTR_MAX == 0xffffffffffffffffULL)  
auto const arch_suffix = "x64";  
#else  
auto const arch_suffix = "x32";  
#endif  
  
int main()  
{  
    try  
    {  
        using bgr121_ref_t = const bit_aligned_pixel_reference<boost::uint8_t, boost::mpl::vector3_c<int, 1, 2, 1>, bgr_layout_t, true>;  
        using bgr121_image_t = image<bgr121_ref_t, false>;  
        using dims_t = bgr121_image_t::point_t;  
  
        auto const this_path = fs::canonical(fs::path(__FILE__).parent_path());  
  
        auto const max_dim = 8; // modify if you need  
        for (int i = 2; i < max_dim + 1; i += 1)  
        {  
            auto const log_path = this_path / fs::path("test_");  
            std::ostringstream os;  
            os << log_path << i << "x" << i << "_" << arch_suffix << "_" << config_suffix << ".log";  
            auto log = os.str();  
  
            // if (i == 5) may randomly crash in opt, in gil::image<..>::deallocate  
  
            // test  
            std::ofstream ofs(log );  
            std::cerr << log << std::endl;  
            image_test test(ofs);  
            dims_t dims(i, i);  
            test.basic_test<bgr121_image_t>(dims);  
        }  
    }  
    catch (std::runtime_error const& e)  
    {  
        std::cerr << e.what() << std::endl;  
    }  
    return 0;  
}  
```  
  
### Results  
  
Dobule-check comparing the calculated CRC and hex dump of the test image:  
  
* 32-bit build is fine  
  
```  
D:\dev\boost-gil-workshop\crc (image-crc-bug-test)  
λ for /L %i in (2, 1, 4) do diff -Nua test_%ix%i_x32_dbg.log test_%ix%i_x32_opt.log  
  
diff -Nua test_2x2_x32_dbg.log test_2x2_x32_opt.log  
  
diff -Nua test_3x3_x32_dbg.log test_3x3_x32_opt.log  
  
diff -Nua test_4x4_x32_dbg.log test_4x4_x32_opt.log  
```  
  
* 64-bit reproduces the bug  
  
```  
D:\dev\boost-gil-workshop\crc (image-crc-bug-test)  
λ for /L %i in (2, 1, 4) do diff -Nua test_%ix%i_x64_dbg.log test_%ix%i_x64_opt.log  
  
diff -Nua test_2x2_x64_dbg.log test_2x2_x64_opt.log  
  
diff -Nua test_3x3_x64_dbg.log test_3x3_x64_opt.log  
--- test_3x3_x64_dbg.log        2018-03-22 17:31:24.330980200 +0100  
+++ test_3x3_x64_opt.log        2018-03-22 16:16:02.111684000 +0100  
@@ -1,4 +1,4 @@  
 Checksum (red  fill):          23a6f403  
 FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000  
-Checksum (blue line):          2e4950b4  
-FF0000FF00000000FFFF00000000FFFF00000000FFFF0000FF0000  
+Checksum (blue line):          ff43598  
+FF0000FF00000000FFFF0000FF0000FF00000000FFFF0000FF0000  
  
diff -Nua test_4x4_x64_dbg.log test_4x4_x64_opt.log  
--- test_4x4_x64_dbg.log        2018-03-22 17:31:24.357974200 +0100  
+++ test_4x4_x64_opt.log        2018-03-22 16:16:02.138165400 +0100  
@@ -1,4 +1,4 @@  
 Checksum (red  fill):          b803c24c  
 FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000  
-Checksum (blue line):          7313468d  
-FF0000FF0000FF00000000FFFF0000FF00000000FFFF0000FF00000000FFFF0000FF00000000FFFF0000FF0000FF0000  
+Checksum (blue line):          8570c3bc  
+FF00000000FFFF0000FF00000000FFFF0000FF0000FF0000FF00000000FFFF0000FF00000000FFFF0000FF0000FF0000  
```  
  
As you can see, the bug can be reproduced with **image as small as 3x3**:  
  
![image-crc-bug-hex](https://user-images.githubusercontent.com/80741/37787068-733c7e0c-2dfe-11e8-8bea-c99497aad604.png)  
  
Finally, running the optimised build for **image 5x5** randomly crashes near `gil::image<..>::deallocate` call.  
  
I do not see clear reason to blame the `boost::crc_32__type`; I suspect bug in `image_view<>::locator` or `image_view` itself, of their specialisations.

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2018-03-22 17:15:42 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-375386498  

Wow, excellent detective work, Dr. Watson ! :-)  
What does the last screenshot above actually show ? What are the hex numbers between the lines starting with "Checksum" ? (There are two lines that differ !)  
  
An interesting (though not necessarily important) question is whether this bug also exists on the `master` branch, as it doesn't seem to be related to the IO extension. (This would mean it's not a regression, and so we don't have to include it in this milestone. But then again, we are under no time pressure here, as the next Boost release to which we'd like to merge this is still half a year away...)

---

## Comment 7

> Username: mloskot  
> Created at: 2018-03-22 17:29:22 UTC  
> Updated at: 2018-06-23 10:32:04 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-375391570  

@stefanseefeld   
> What are the hex numbers between the lines starting with "Checksum" ?  
  
This is hex-encoded binary of test image  
  
> What does the last screenshot above actually show ?  
  
1. Fill empty *3x3* RGB image with red `FF0000` pixels  
  
```  
Checksum (red  fill):		23a6f403   <--- correct checksum  
FF0000FF0000FF0000  
FF0000FF0000FF0000  
FF0000FF0000FF0000  
```  
  
2. **Expected**: draw blue diagonal from top-right to bottom-left (flipping red to blue `0000FF`)  
  
```  
Checksum (blue line):		2e4950b4   <--- correct checksum  
FF0000FF00000000FF  
FF00000000FFFF0000  
0000FFFF0000FF0000  
```  
  
3. **Actual**: draw blue diagonal  
  
```  
Checksum (blue line):		ff43598   <--- NOT correct checksum  
FF0000FF00000000FF  
FF0000FF0000FF0000  
0000FFFF0000FF0000  
```  
  
Makes sense?

---

## Comment 8

> Username: mloskot  
> Created at: 2018-03-22 17:36:05 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-375394040  

@stefanseefeld   
> An interesting (though not necessarily important) question is whether this bug also exists on the master branch, as it doesn't seem to be related to the IO extension.  
  
There is no use of GIL IO used at all. I've just removed the confusing `gil/extension/dynamic_image/dynamic_image_all.hpp`. Here is full project https://github.com/mloskot/boost-gil-workshop/tree/image-crc-bug-test/crc

---

## Comment 9

> Username: stefanseefeld  
> Created at: 2018-03-22 17:41:43 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-375396094  

OK, I see, that makes sense ! Unless you think you could fix the issue quickly, I suggest you add that test to GIL's `test/` (or perhaps we should create a `test/regressions/` subdirectory, for such cases where we want to track bugs but don't want to "pollute" the main test suite with arbitrary additions.)  
Just a suggestion...

---

## Comment 10

> Username: mloskot  
> Created at: 2018-03-22 20:20:28 UTC  
> Updated at: 2018-06-23 10:34:36 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-375443914  

> Unless you think you could fix the issue quickly  
  
No idea really, it's still a cowboy shoot-out.  
> I suggest you add that test to GIL's test/ (or perhaps we should create a test/regressions/ subdirectory,  
> for such cases where we want to track bugs  
  
This is a good idea, I will work out something.  
  
### Test 2  
  
BTW, an interesting case is drawing blue line on red fill: checksums match, but the x64 release crashes:  
  
![image-crc-bug-blue-blue](https://user-images.githubusercontent.com/80741/37796066-976a4058-2e16-11e8-80c8-19de8003e5a5.png)

---

## Comment 11

> Username: mloskot  
> Created at: 2018-03-22 21:48:53 UTC  
> Updated at: 2018-06-23 10:38:06 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-375468905  

### Test 3  
  
Updated to [Test variety of **bit-aligned pixel** configurations: bgr111, bgr121, bgr222, bgr232, bgr233](https://github.com/mloskot/boost-gil-workshop/commit/fe634604d04670bf34022e430e98c8ef30282167) and the results are even more interesting.  
  
The checksum is calculated for **regular** RGB8 view of the each of the five bit-aligned pixel image variations: bgr111, bgr121, bgr222, bgr232, bgr233.  
  
### Results  
  
On the screenshot, look how the checksum randomly changes (red marked):  
  
![image-crc-bug-bit-aligned-pixel](https://user-images.githubusercontent.com/80741/37799563-5814b906-2e20-11e8-815f-e1a121817398.png)  
  
**ASSUMPTION**:   
Given plain red-coloured image, regardless of how many bits per colour in bit-aligned pixel (ie. bgr111, bgr121, bgr222, bgr232, bgr233), if viewed as regular RGB8, it should always be `FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000` and the checksum should always match.  
  
The above means that ALL red-coloured images within matching image size (ie. 2x2, 3x3, etc.) must have exactly the same checksum.  
  
Now, let's forget about the case drawing blue diagonal and only look at the red-coloured images:  
  
- from [test_3x3_x64_dbg.log](https://github.com/mloskot/boost-gil-workshop/blob/fe634604d04670bf34022e430e98c8ef30282167/crc/test_3x3_x64_dbg.log) (NOTICE: this is 64-bit **debug** build).  
  
```  
Checksum (bgr111: red  fill):	23a6f403   <- rgb8  
FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000   OK  
Checksum (bgr121: red  fill):	23a6f403   <- rgb8  
FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000   OK  
Checksum (bgr222: red  fill):	23a6f403   <- rgb8  
FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000   OK  
Checksum (bgr232: red  fill):	522cfc71   <- rgb8  
FF0000FF0000FF0000FF0000FF0000FF0000550000FF0000FF0000   BUG  
Checksum (bgr233: red  fill):	1e81d049   <- rgb8  
230000230000230000230000230000230000230000230000230000   BUG  
```  
  
* and from [test_3x3_x64_opt.log](https://github.com/mloskot/boost-gil-workshop/blob/fe634604d04670bf34022e430e98c8ef30282167/crc/test_3x3_x64_opt.log) (NOTICE: this is 64-bit **release** build)  
  
```  
Checksum (bgr111: red  fill):	23a6f403   <- rgb8  
FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000   OK  
Checksum (bgr121: red  fill):	23a6f403   <- rgb8  
FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000   OK  
Checksum (bgr222: red  fill):	23a6f403   <- rgb8  
FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000FF0000   OK  
Checksum (bgr232: red  fill):	522cfc71   <- rgb8  
FF0000FF0000FF0000FF0000FF0000FF0000550000FF0000FF0000   BUG (== as above)  
Checksum (bgr233: red  fill):	1e81d049   <- rgb8  
230000230000230000230000230000230000230000230000230000   BUG (== as above)  
```  
  
An important observation is that both 64-bit builds, debug and release, reproduce the same erroneous result! So, it seems safe to assume the bug is NOT specific to optimised compilation for 64-bit, as I initially suspected.  
  
I suspect, the bug is in `bit_aligned_pixel_reference` or collaborating parties.  
  
### MWE  
  
Finally, the research above allows to minimise the whole test to this:  
  
```  
template <typename Image>  
void bit_aligned_pixel_image_test(typename Image::point_t const& dims, std::string const& bgr_suffix)  
{  
    Image img(dims);  
    auto const& img_view = view(img);  
  
    // colors  
    rgb8_pixel_t red8(255, 0, 0), blue8(0, 0, 255);  
    typename Image::view_t::value_type red, blue;  
    color_convert(red8, red);  
    color_convert(blue8, blue);  
      
    fill(img_view.begin(), img_view.end(), red);  
    check_view(img_view, bgr_suffix +": red  fill");  
}  
```  
  
for each `Image` in `bgr111_img_t`, `bgr121_img_t`, `bgr222_img_t`, `bgr232_img_t`, `bgr233_img_t`.

---

## Comment 12

> Username: stefanseefeld  
> Created at: 2018-03-27 23:08:22 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-376704714  

Has the error only ever been observed with MSVC ? I'm trying to figure out whether it would be worth for me to have a look into it, on Linux (either gcc or clang).

---

## Comment 13

> Username: mloskot  
> Created at: 2018-03-27 23:16:05 UTC  
> Updated at: 2018-03-29 08:52:32 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-376706264  

I only reproduced this particular issue with MSVC.  
  
But, enabling `variant=release` wtth GCC (see #50), leads to failures of `gil/test/channel.cpp` tests which I have not investigated yet.

---

## Comment 14

> Username: chhenning  
> Created at: 2018-03-31 17:01:15 UTC  
> Updated at: 2018-06-23 11:17:06 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-377707590  

### MWE v1 for Test 2  
  
```  
#define _CRT_SECURE_NO_WARNINGS  
#include <boost/gil/gil_all.hpp>  
#include <boost/gil/extension/io/bmp_all.hpp>  
using namespace boost::gil;  
  
void draw_line()  
{  
    using ref_t = const bit_aligned_pixel_reference<boost::uint8_t, boost::mpl::vector3_c<int, 1, 2, 1>, bgr_layout_t, true>;  
    using ptr_t = bit_aligned_pixel_iterator<ref_t>;  
    using pixel_t =  std::iterator_traits<ptr_t>::value_type;  
    using bgr121_image_t = image<ref_t, false>;  
  
    bgr121_image_t img(100, 100);  
    fill_pixels(view(img), pixel_t(0,0,1));  
  
    auto loc = view(img).xy_at(0, view(img).height() - 1);  
    for (int y = 0; y < view(img).height(); ++y)  
    {  
        *loc = pixel_t(1, 0, 0);  
        ++loc.x();  
        loc.y()--;  
    }  
  
    auto v = color_converted_view<rgb8_pixel_t>(view(img));  
    write_view("img.bmp", v, bmp_tag());  
}  
  
int main()  
{  
    draw_line();  
}  
```  
  
Debug:  
![debug](https://user-images.githubusercontent.com/3671576/38165566-7d7a92c8-34e3-11e8-8659-3c276a6ae35a.png)  
  
Release:  
![release](https://user-images.githubusercontent.com/3671576/38165569-932014f4-34e3-11e8-9443-8a37a84990b3.png)

---

## Comment 15

> Username: mloskot  
> Created at: 2018-03-31 20:48:14 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-377722393  

@chhenning Thanks for this. I think it may confirm my assumption from https://github.com/boostorg/gil/issues/49#issuecomment-375383391 about bug around the locator.

---

## Comment 16

> Username: chhenning  
> Created at: 2018-03-31 21:38:37 UTC  
> Updated at: 2018-06-23 11:17:20 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-377725324  

### MWE v2 for Test 2 (`minimal_chhenning.cpp`)  
  
```  
#define _CRT_SECURE_NO_WARNINGS 1  
#include <boost/gil/gil_all.hpp>  
#include <boost/gil/extension/io/bmp.hpp>  
using namespace boost::gil;  
  
int main()  
{  
    using ref_t = const bit_aligned_pixel_reference<boost::uint8_t, boost::mpl::vector3_c<int, 1, 2, 1>, bgr_layout_t, true>;  
    using ptr_t = bit_aligned_pixel_iterator<ref_t>;  
    using pixel_t = std::iterator_traits<ptr_t>::value_type;  
    using bgr121_image_t = image<ref_t, false>;  
  
    // works  
    {  
        bgr121_image_t a(10, 10);  
        fill_pixels(view(a), pixel_t(0, 0, 1));  
  
        auto loc = view(a).xy_at(0, 0);  
        for (int y = 0; y < view(a).height(); ++y)  
        {  
            *loc = pixel_t(1, 0, 0);  
            ++loc.x();  
            ++loc.y();  
        }  
  
        auto v = color_converted_view<rgb8_pixel_t>(view(a));  
        write_view("a.bmp", v, bmp_tag());  
    }  
    // doesn't work  
    {  
        bgr121_image_t b(10, 10);  
        fill_pixels(view(b), pixel_t(0, 0, 1));  
  
        auto loc = view(b).xy_at(0, view(b).height() - 1);  
        for (int y = 0; y < view(b).height(); ++y)  
        {  
            *loc = pixel_t(1, 0, 0);  
            ++loc.x();  
            --loc.y();  
        }  
  
        auto v = color_converted_view<rgb8_pixel_t>(view(b));  
        write_view("b.bmp", v, bmp_tag());  
    }  
}  
```

---

## Comment 17

> Username: mloskot  
> Created at: 2018-06-23 11:30:01 UTC  
> Updated at: 2018-06-23 12:43:54 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-399668612  

## Running `minimal_chhenning.cpp` test  
  
Source of `minimal_chhenning.cpp` from https://github.com/boostorg/gil/issues/49#issuecomment-377725324  
  
### VS2017 x64 Release  
  
![image](https://user-images.githubusercontent.com/80741/41808980-20f878fe-76e6-11e8-8540-c79db367f0e2.png)  
  
### clang 5.0 on Ubuntu 18.04 (optimised, with `-fsanitizer=integer`)  
  
```  
$ export UBSAN_OPTIONS=print_stacktrace=1  
$ clang++-5.0 -std=c++11 -Wno-unused -g -fstrict-aliasing -fno-omit-frame-pointer -fsanitize=integer -I/mnt/d/boost.wsl -O2 minimal_chhenning.cpp  
$ file ./a.out  
./a.out: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, with debug_info, not stripped  
$ ./a.out  
```  
  
![image](https://user-images.githubusercontent.com/80741/41809011-b95cebac-76e6-11e8-9397-242b100705f4.png)  
  
### GCC 8 on Ubuntu 18.04 (optimised,  with `-fsanitizer=undefined`,  `integer` checks group not available for GCC)  
  
```  
$ export UBSAN_OPTIONS=print_stacktrace=1  
g++-8 -g -std=c++11 -Wno-unused -fstrict-aliasing -fno-omit-frame-pointer -fsanitize=undefined -I/mnt/d/boost.wsl -O2 minimal_chhenning.cpp  
$ file ./a.out  
./a.out: ELF 64-bit LSB shared object, x86-64, version 1 (GNU/Linux), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=4aeead00f767d589d784fa5fd2f2b6ac228b37f6, with debug_info, not stripped  
./a.out  
```  
  
![image](https://user-images.githubusercontent.com/80741/41809139-a0019ec6-76e8-11e8-83a6-2acc6c8142fa.png)  
  
## Conclusions  
  
* The issues are observable as:   
   * The blue line drawn vertically instead of diagonally  
   * The [checksum test failure on AppVeyor with VS2017 x64 release build](https://ci.appveyor.com/project/stefanseefeld/gil/build/job/d000gjykkqt0t2y8#L961)  
* The issues seem to be only reproducible with VS2017 x64 release/optimized build   
* The [unsigned integer overflows reported by UBSan on Travis](https://travis-ci.org/boostorg/gil/jobs/395655949#L1340) for the checksum test seem to be *false positives* and *seem unrelated* to the issues discussed here:  
```  
boost/gil/extension/dynamic_image/../../channel_algorithm.hpp:344:64:  
  runtime error: unsigned integer overflow: 4294967287 + 128  
    cannot be represented in type 'unsigned int'  
```  
  
**What do we do about this?** Especially regarding the Grand Merge and Boost 1.68?  
  
-----  
  
By the way, it is interesting we are seeing `unsigned integer overflow: 4294967287 + 128  cannot be represented in type 'unsigned int'` for `int8_t` input and `uint8_t` output:  
  
https://github.com/boostorg/gil/blob/d882b844944001f5f94e55aed3faaf651aae3032/include/boost/gil/channel_algorithm.hpp#L343-L345  
  
Looks like the LHS operand of `static_cast<uint32_t>(-9) + 128u` unsigned integer overflows and yields `4294967287 + 128`.  
  
Changing the line `344` from  
```  
return static_cast<uint8_t>(static_cast<uint32_t>(val) + 128u);  
```  
to  
```  
return static_cast<uint8_t>(static_cast<uint32_t>(val) + static_cast<uint64_t>(128u));  
```  
makes the UBSan happy, obviously :) The two return statements are equivalent and should yield the same values. So, the related UBSan failures should be safe to ignore (eg. suppress via regexp in `.ci/blacklist.supp` file).  
  
**Thoughts?**

---

## Comment 18

> Username: chhenning  
> Created at: 2018-06-23 17:13:04 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-399694107  

@mloskot Do you think this is a compiler bug only with VS2017 x64? Should we try VC2015 or VS2017 x32?

---

## Comment 19

> Username: mloskot  
> Created at: 2018-06-23 22:10:02 UTC  
> Url: https://github.com/boostorg/gil/issues/49#issuecomment-399713859  

@chhenning I don't want to speculate about a compiler bug, yet. I think it's more reasonable to rather look for eliminating evidence on _what the issue is not caused by_, than evidence proving actual cause of the problem.  
  
> Chould we try VC2015  
  
I don't have VS2015.   
  
>  or VS2017 x32?  
  
AppVeyor covers it for both, release and debug configurations https://ci.appveyor.com/project/stefanseefeld/gil/build/1.0.397-develop  
  
-----  
  
For the checksum test issue specifically, I think it may be a good idea to try different checksum than CRC. For example, refactor the test to calculate checksum based on MD5 or SHA1 or even something like [GDAL simple 16-bit checksum](https://github.com/OSGeo/gdal/blob/master/gdal/alg/gdalchecksum.cpp). This could help to eliminate either issue in checksum calculation or image data access.
