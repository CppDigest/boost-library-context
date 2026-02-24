# #447 - Broken bit-aligned pixel locator on MSVC++ 64-bit optimized builds only [Open]

> Username: mloskot  
> Created at: 2020-03-10 23:37:04 UTC  
> Updated at: 2020-03-14 23:34:19 UTC  
> Url: https://github.com/boostorg/gil/issues/447  

Broken bit-aligned pixel locator on MSVC++ 64-bit optimized builds only  
  
Over the last few months, intermittently I've been chasing a peculiar bug that has been revealing itself in failing [checksum tests in test/legacy/image.cpp](https://github.com/boostorg/gil/blob/2ff2d31895dae665ef3e05d0496bc082470e229c/test/legacy/image.cpp#L373-L381) since we set up the CI builds with MSVC++ compiler or started testing GIL using `b2 toolset=msvc variant=release address-model=64` on regular basis.  
  
The peculiarity of this bug is that it has not been observed using any of these compilers:  
  
  - GCC: 4.8, 4.9, 5, 6, 7, 8, 9  
  - Clang: 3.9, 4.0, 5.0, 6.0, 7, 8, 9, 10  
  - XCode: 8.3, 9.0, 9.1, 9.2, 9.3, 9.4, 10  
  
Finally, I have managed to reproduce the problem with minimal working example (see [minimal_test.cpp](#minimal-working-example)) which leaves out the checksum calculation and narrows the scope of the bug down to the `xy_locator` and advances of the iterators.  
  
### Actual Behavior 1  
  
#### VS2017 msvc-14.1  
  
If one looks at the recent **VS2017 build job** on AppVeyor [TOOLSET=msvc-14.1, ARCH=x86_64, VARIANT=release](https://ci.appveyor.com/project/stefanseefeld/gil/build/job/sygh92699xhbfxhk), which n.b. is allowed to fail due to the mysterious nature of the issue, one will notice this output is logged for the `test/legacy/image.cpp`:  
  
```console  
testing.capture-output bin.v2\libs\gil\test\legacy\image.test\msvc-141\rls\adrs-mdl-64\async-excpt-on\cxstd-14-iso\thrd-mlt\image.run  
====== BEGIN OUTPUT =====  
...  
checking checksum for bgr121_basic_red_x (crc=7f6e24e7)  
Checking checksum for bgr121_basic_white_x (crc=e4aaa1d3)  
Checking checksum for bgr121_views_original (crc=7e5bb87d)  
Checking checksum for bgr121_views_cropped (crc=43305e15)  
Checking checksum for bgr121_views_gray8 (crc=bbabe219)  
Checking checksum for bgr121_views_my_gray8 (crc=3086d22f)  
Checking checksum for bgr121_views_transpose (crc=da2ff80)  
Checking checksum for bgr121_views_rot180 (crc=68f37202)  
Checksum error in bgr121_views_90cw (crc=8ffd852b != 8565efd8)  
Unknown exception  
   
EXIT STATUS: 1   
====== END OUTPUT =  
  
    set status=0  
  
...failed testing.capture-output bin.v2\libs\gil\test\legacy\image.test\msvc-14.1\rls\adrs-mdl-64\async-excpt-on\cxstd-14-iso\thrd-mlt\image.run...  
```  
  
#### VS2019 msvc-14.2  
  
Local builds using `b2 toolset=msvc-14.2 variant=release address-model=64` fail for `bgr121_image_t` but at earlier stage:  
  
```  
Checksum error in bgr121_basic_red_x (crc=85c86c53 != 7f6e24e7)  
Unknown exception  
```  
  
There clearly is something fishy going on when using MSVC++ optimiser.  
  
### Actual Behavior 2  
  
1. Create 3x3 image based on bit-aligned pixel (`bgr121_image_t`)  
2. Fill image with red  
3. Draw blue diagonal  
  
```cpp  
bgr121_image_t img(3, 3);  
// ... fill img with `bgr121_red` pixels  
  
// draw blue diagonal  
auto v = view(img);  
auto loc = v.xy_at(0, v.height() - 1);  
for (std::ptrdiff_t y = 0; y < v.height(); ++y)  
{  
    *loc = bgr121_blue; // set blue pixel value  
    ++loc.x();          // INCREMENT X FIRST  
    --loc.y();  
}  
```  
  
If the loop like this is compiled using either  
- `b2 toolset=msvc variant=release address-model=64 optimization=space` (/O1 /Ob2)  
- `b2 toolset=msvc variant=release address-model=64 optimization=speed` (/O2 /Ob2)  
  
then pixels of the blue diagonal are messed up.  
  
If the loop is compiled without optimization using either  
- `b2 toolset=msvc variant=debug address-model=64` (/Od /Ob2)  
- `b2 toolset=msvc variant=release address-model=64 optimization=off` (/Od /Ob2)  
  
then pixels of the blue diagonal are correct.  
  
### Actual Behavior 3  
  
Take the `for` loop above, but reorder lines incrementing `x_iterator` and `y_itertor`:  
  
```cpp  
for (std::ptrdiff_t y = 0; y < v.height(); ++y)  
{  
    *loc = bgr121_blue; // set blue pixel value  
    --loc.y();  
    ++loc.x();          // PRE-INCREMENT X SECOND  
}  
```  
  
or  
  
```cpp  
for (std::ptrdiff_t y = 0; y < v.height(); ++y)  
{  
    *loc = bgr121_blue; // set blue pixel value  
    --loc.y();  
    ++loc.x();          // POST-INCREMENT X SECOND  
}  
```  
  
compile with the optimization and observe the blue line is drawn correctly.  
  
Similarly, if the loop is manually unrolled, the bug disappears (see [minimal_test.cpp](#minimal-working-example)).  
  
### Expected Behavior  
  
1. The checksum is calculated correctly.  
2. Pixels of the blue diagonal are correct regardless of the optimisation.  
2. Pixels of the blue diagonal are correct regardless of the order of which iterator, X or Y, is incremented first.  
  
### References  
  
- The issue posted to Boost developers list https://lists.boost.org/Archives/boost/2020/03/248397.php  
  
### Minimal Working Example  
  
Complete project at https://github.com/mloskot/boost-gil-checksum-bug with GitHub Actions configured to build and run this `minimal_test.cpp` test:  
  
```cpp  
// minimal_test.cpp  
#include <boost/version.hpp>  
#include <boost/core/lightweight_test.hpp>  
#if BOOST_VERSION < 106900  
#include <boost/gil/gil_all.hpp>  
#else  
#include <boost/gil.hpp>  
#endif  
namespace gil = boost::gil;  
  
#if BOOST_VERSION > 107200  
using channel_sizes_t = boost::mp11::mp_list_c<int, 1, 2, 1>;  
#else  
using channel_sizes_t = boost::mpl::vector3_c<int, 1, 2, 1>;  
#endif  
  
using bgr121_ref_t = gil::bit_aligned_pixel_reference  
<  
    std::uint8_t,  
    channel_sizes_t,  
    gil::bgr_layout_t,  
    true  
> const;  
using bgr121_image_t = gil::image<bgr121_ref_t, false>;  
using bgr121_view_t = typename bgr121_image_t::view_t;  
using bgr121_pixel_t = typename bgr121_view_t::value_type;  
  
bgr121_pixel_t bgr121_red(0), bgr121_blue(0);  
  
void fill_image_red(bgr121_image_t& img)  
{  
    gil::rgb8_pixel_t red8(255, 0, 0);  
    gil::rgb8_pixel_t blue8(0, 0, 255);  
    gil::color_convert(red8, bgr121_red);  
    gil::color_convert(blue8, bgr121_blue);  
  
    auto v = view(img);  
    std::fill(v.begin(), v.end(), bgr121_red);  
  
    for (auto it = view(img).begin().x(), end = view(img).end().x(); it != end; ++it)  
        BOOST_TEST(*it == bgr121_red);  
}  
  
void test_draw_with_xy_locator_loop_fail(std::ptrdiff_t w, std::ptrdiff_t h)  
{  
    bgr121_image_t img(w, h);  
    fill_image_red(img);  
    {  
        auto v = view(img);  
        auto loc = v.xy_at(0, v.height() - 1);  
        for (std::ptrdiff_t y = 0; y < v.height(); ++y)  
        {  
            *loc = bgr121_blue;  
            // OPTION 1 FAILS  
            ++loc.x(); // PRE-INCREMENT X FIRST  
            --loc.y();  
  
            // OPTION 2 FAILS TOO  
            //loc.x()++; // POST-INCREMENT X FIRST  
            //loc.y()--;  
  
            // OPTION 3 FAILS TOO  
            //auto& x_it = loc.x(); // PRE-INCREMENT NAMED X FIRST  
            //++x_it;  
            //auto& y_it = loc.y();  
            //--y_it;  
        }  
    }  
    {  
        auto it = view(img).begin().x();  
        // row 0  
        BOOST_TEST(*it == bgr121_red); ++it;  
        BOOST_TEST(*it == bgr121_red); ++it;  
        BOOST_TEST(*it == bgr121_blue); ++it;  
        // row 1  
        BOOST_TEST(*it == bgr121_red); ++it;  
        BOOST_TEST(*it == bgr121_blue); ++it; // FAIL  
        BOOST_TEST(*it == bgr121_red); ++it;  
        // row 2  
        BOOST_TEST(*it == bgr121_blue); ++it;  
        BOOST_TEST(*it == bgr121_red); ++it;  
        BOOST_TEST(*it == bgr121_red);  
    }  
}  
  
void test_draw_with_xy_locator_loop_good(std::ptrdiff_t w, std::ptrdiff_t h)  
{  
    bgr121_image_t img(w, h);  
    fill_image_red(img);  
    {  
        auto v = view(img);  
        auto loc = v.xy_at(0, v.height() - 1);  
        for (std::ptrdiff_t y = 0; y < v.height(); ++y)  
        {  
            *loc = bgr121_blue;  
            // OPTION 1 WORKS  
            --loc.y();  
            ++loc.x(); // PRE-INCREMENT X SECOND  
  
            // OPTION 2  WORKS TOO  
            //loc.y()--;  
            //loc.x()++; // POST-INCREMENT X SECOND  
        }  
    }  
    {  
        auto it = view(img).begin().x();  
        // row 0  
        BOOST_TEST(*it == bgr121_red); ++it;  
        BOOST_TEST(*it == bgr121_red); ++it;  
        BOOST_TEST(*it == bgr121_blue); ++it;  
        // row 1  
        BOOST_TEST(*it == bgr121_red); ++it;  
        BOOST_TEST(*it == bgr121_blue); ++it;  
        BOOST_TEST(*it == bgr121_red); ++it;  
        // row 2  
        BOOST_TEST(*it == bgr121_blue); ++it;  
        BOOST_TEST(*it == bgr121_red); ++it;  
        BOOST_TEST(*it == bgr121_red);  
    }  
}  
  
void test_draw_with_xy_locator_step_good(std::ptrdiff_t w, std::ptrdiff_t h)  
{  
    bgr121_image_t img(w, h);  
    fill_image_red(img);  
    {  
        auto v = view(img);  
  
        auto loc = v.xy_at(0, v.height() - 1);  
        *loc = bgr121_blue; // red red blue  
        ++loc.x();  
        --loc.y();  
        *loc = bgr121_blue; // red blue red  
        ++loc.x();  
        --loc.y();  
        *loc = bgr121_blue; // blue red red  
        ++loc.x();  
        --loc.y();  
    }  
    {  
        auto it = view(img).begin().x();  
        // row 0  
        BOOST_TEST(*it == bgr121_red); ++it;  
        BOOST_TEST(*it == bgr121_red); ++it;  
        BOOST_TEST(*it == bgr121_blue); ++it;  
        // row 1  
        BOOST_TEST(*it == bgr121_red); ++it;  
        BOOST_TEST(*it == bgr121_blue); ++it;  
        BOOST_TEST(*it == bgr121_red); ++it;  
        // row 2  
        BOOST_TEST(*it == bgr121_blue); ++it;  
        BOOST_TEST(*it == bgr121_red); ++it;  
        BOOST_TEST(*it == bgr121_red);  
    }  
}  
  
int main()  
{  
    try  
    {  
        test_draw_with_xy_locator_loop_fail(3, 3);  
        test_draw_with_xy_locator_loop_good(3, 3);  
        test_draw_with_xy_locator_step_good(3, 3);  
    }  
    catch (std::exception const& e)  
    {  
        std::cerr << e.what() << std::endl;  
  
    }  
    return boost::report_errors();  
}  
```  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version: MSVC++ all with C++11 support  
- Build settings: 64-bit optimised (release)  
- Version: Boost 1.67 through 1.72 to current GIL `develop` branch
