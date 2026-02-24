# #620 - for_each_pixel return value is incorrect for not 1d traversable view [Closed]

> Username: sdebionne  
> Created at: 2021-07-16 14:58:46 UTC  
> Updated at: 2022-03-01 13:35:21 UTC  
> Closed at: 2022-03-01 13:35:21 UTC  
> Url: https://github.com/boostorg/gil/issues/620  

### Actual behavior  
  
I believe the bug was introduced in #139. The behavior is an incorrect return value from `for_each_pixel()` when the input view is not 1d traversable.  
  
The (removed) re-assignement was here to cope with this use-case.  
  
### Expected  behavior  
  
In the following loop  
  
```cpp  
        for (std::ptrdiff_t y = 0; y < view.height(); ++y)  
            std::for_each(view.row_begin(y), view.row_end(y), fun);  
```  
  
the `fun` should be carried over from `y` interation to the other, not using a fresh copy of the input `fun`.  
  
### C++ Minimal Working Example  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
Here are additional test cases for the `for_each_pixel` algo, the `test_function_object_not_1d_traversable` one is currently failing:  
  
```cpp  
#include <boost/gil/algorithm.hpp>  
#include <boost/gil/image.hpp>  
#include <boost/gil/image_view_factory.hpp>  
  
#include <boost/core/lightweight_test.hpp>  
  
namespace gil = boost::gil;  
  
struct accumulator  
{  
    void operator()(gil::gray8_pixel_t const& p) {  
        sum += gil::at_c<0>(p);  
    }  
  
    int sum;  
};  
  
void test_function_object_1d_traversable()  
{  
    gil::gray8_pixel_t const gray128(128);  
    gil::gray8_image_t image(2, 2, gray128);  
  
    accumulator acc;  
    acc = gil::for_each_pixel(  
        gil::const_view(image),  
        acc  
    );  
    BOOST_TEST_EQ(acc.sum, 2 * 2 * 128);  
}  
  
void test_function_object_not_1d_traversable()  
{  
    gil::gray8_pixel_t const gray128(128);  
    gil::gray8_image_t image(4, 4, gray128);  
  
    accumulator acc;  
    acc = gil::for_each_pixel(  
        gil::subimage_view(gil::const_view(image), gil::point_t{1, 1}, gil::point_t{2, 2}),  
        acc  
    );  
    BOOST_TEST_EQ(acc.sum, 2 * 2 * 128);  
}  
  
int main()  
{  
    test_function_object_1d_traversable();  
    test_function_object_not_1d_traversable();  
  
    return ::boost::report_errors();  
}  
```  
  
See https://godbolt.org/z/a9vdMq43n

---

## Comment 1

> Username: sdebionne  
> Created at: 2021-07-16 15:04:15 UTC  
> Url: https://github.com/boostorg/gil/issues/620#issuecomment-881516392  

I believe that the fix is in #187, reopening it.
