# #432 - image_view::col_end always asserts [Closed]

> Username: evansds  
> Created at: 2020-02-07 10:20:22 UTC  
> Updated at: 2020-03-15 16:15:48 UTC  
> Closed at: 2020-03-15 16:15:48 UTC  
> Url: https://github.com/boostorg/gil/issues/432  

### Actual behavior  
  
After updating to boost 1.72.0, calling `image_view::col_end(y)` always causes an assertion.  
  
`image_view::xy_at()` asserts that `y < height()`, but it's called with `y = height()` when creating a column end iterator.  
  
### Expected  behavior  
  
No assertion.  
  
### C++ Minimal Working Example  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
```cpp  
std::vector<int> v(50);  
auto view = boost::gil::interleaved_view(10, 5, v.data(), 10 * sizeof(int));  
auto it = view.col_end(0);  // asserts  
```  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version: VS2017  
- Build settings: Debug  
- Version (Git ref or `<boost/version.hpp>`): 1.72.0

---

## Comment 1

> Username: mloskot  
> Created at: 2020-02-21 11:27:10 UTC  
> Updated at: 2020-02-21 11:27:37 UTC  
> Url: https://github.com/boostorg/gil/issues/432#issuecomment-589614697  

@evansds Thanks for the report.  
  
This is related to changes in #344 which also adds TODOs needing clarification

---

## Comment 2

> Username: simmplecoder  
> Created at: 2020-03-11 18:27:01 UTC  
> Updated at: 2020-03-11 18:28:24 UTC  
> Url: https://github.com/boostorg/gil/issues/432#issuecomment-597794636  

I was not able to compile the example code due to missing `memunit_xxx` functions, because there are no specializations for `int`. I was able to compile the following code however:  
  
```cpp  
#include <boost/gil/typedefs.hpp>  
#include <boost/gil/image_view.hpp>  
#include <boost/gil/image_view_factory.hpp>  
#include <vector>  
  
using pixel_type = boost::gil::rgb8_pixel_t;  
  
int main()   
{  
    std::vector<pixel_type> v(50);  
    auto view = boost::gil::interleaved_view(10, 5, v.data(), 10 * sizeof(pixel_type));  
    auto it = view.col_end(0);  // asserts  
}  
```  
The problematic line is this one: https://github.com/boostorg/gil/blob/2ff2d31895dae665ef3e05d0496bc082470e229c/include/boost/gil/image_view.hpp#L295  
  
Changing  
  
```cpp  
        BOOST_ASSERT(y < height());  
```  
  
To  
  
```cpp  
        BOOST_ASSERT(y <= height());  
```  
lets the program finish without errors, but the change may be erroneous. From the first sight, it looks like adding the `=` will make it correct, as specifying `(width() - 1) * height()` will still be inbound of the underlying container.  
  
/cc @mloskot  @stefanseefeld , should I just submit PR with a sprinkle of tests and changing that one line, or should I investigate further?

---

## Comment 3

> Username: mloskot  
> Created at: 2020-03-11 18:43:00 UTC  
> Url: https://github.com/boostorg/gil/issues/432#issuecomment-597802815  

Since,   
- originally there were no assertions  
- [it was me who added them](https://github.com/mloskot/gil/blame/a2bb8782ba138aaf6afdde8723d198f907033aa3/include/boost/gil/image_view.hpp#L291-L295), with comment of doubt  
- assertions to check user-specified input are generally a questionable practice  
  
I'd simply refine the assert as you suggest, PR it and kill it.  
A minimal test case is always nice to have though, if there is none yet.

---

## Comment 4

> Username: mloskot  
> Created at: 2020-03-14 17:08:11 UTC  
> Url: https://github.com/boostorg/gil/issues/432#issuecomment-599100411  

@simmplecoder Are you going to submit PR for this?

---

## Comment 5

> Username: simmplecoder  
> Created at: 2020-03-15 09:04:56 UTC  
> Url: https://github.com/boostorg/gil/issues/432#issuecomment-599184702  

@mloskot , there is a bit of panic in my country due to ncov2019 being declared pandemic. I had some paperwork to go through and have a bit more ahead.  
  
I would like to add some tests, but it seems like boost lightweight tests do not interoperate well with `BOOST_ASSERT`. Should I just add the tests regardless of how they report errors? I believe it is possible to set handlers on `BOOST_ASSERT`, but it will be in the core code, which is undesirable.

---

## Comment 6

> Username: simmplecoder  
> Created at: 2020-03-15 09:26:07 UTC  
> Url: https://github.com/boostorg/gil/issues/432#issuecomment-599186558  

@evansds , could you please try to run my branch and see if it solved the problem? #450   
  
```  
make test_core_image_view_iteration  
./bin/test_core_image_view_iteration  
```

---

## Comment 7

> Username: mloskot  
> Created at: 2020-03-15 16:15:48 UTC  
> Url: https://github.com/boostorg/gil/issues/432#issuecomment-599230924  

I merged @simmplecoder 's fix in #450   
  
I will look into any other spots that may need the assert to be refined. I will also update the tests.
