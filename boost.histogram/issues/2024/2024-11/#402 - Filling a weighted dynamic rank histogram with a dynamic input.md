# #402 - Filling a weighted dynamic rank histogram with a dynamic input [Closed]

> Username: dguest  
> Created at: 2024-11-03 21:45:31 UTC  
> Updated at: 2025-06-05 21:14:22 UTC  
> Closed at: 2025-06-05 21:14:22 UTC  
> Url: https://github.com/boostorg/histogram/issues/402  

I might have missed something, but I can't figure out a clean way to fill a dynamic rank histogram with inputs that also dynamic without using `fill` and some slightly weird reformatting for the inputs.  
  
Specifically I have something like this  
  
```C++  
  namespace bh = boost::histogram;  
  using dax_t = bh::axis::regular<double>;  
  std::vector<dax_t> axes {  
    dax_t(1, -0.5, 0.5, "ax0"),  
    dax_t(1, -0.5, 0.5, "ax1")  
  };  
  auto hdyn = bh::make_weighted_histogram(axes);  
  // seems we need to do some funny organization to make weighting  
  // work with dynamic axes.  
  std::vector<std::vector<double>> vals { {0}, {0} };  
  hdyn.fill(vals, bh::weight(0.5));  
```  
If I try to fill with a `std::vector<double> vals {0, 0};` and using `hdyn(vals, bh::weight(0.5));` I get a `number of arguments != histogram rank` exception. Of course calling `hdyn(0, 0, bh::weight(0.5))` works fine, but that sort of limits the dynamic part of the dynamic axes.  
  
To be clear, what I have works fine and everything else about this library is amazing. And I don't have much of a use case for dynamic axes anyway, so this is more of a curiosity.

---

## Comment 1

> Username: HDembinski  
> Created at: 2025-06-05 19:30:46 UTC  
> Updated at: 2025-06-05 20:48:23 UTC  
> Url: https://github.com/boostorg/histogram/issues/402#issuecomment-2945849843  

You are right, there is indeed no example for that combination in the user guide, but it is explained correctly here:  
  
https://www.boost.org/doc/libs/master/libs/histogram/doc/html/doxygen/classboost_1_1histogram_1_1histogram.html#doxygen.classboost_1_1histogram_1_1histogram_1a7378820b8916e99560f19707d04ff7a5  
  
There are also tests in histogram_fill_test.cpp regarding that, but the gist is that you are right. In vals, the first dimension corresponds to the number of axes, and the second dimension goes over the samples.   
  
If you have to transform the data anyway, then you can indeed use `std::vector<std::vector<double>>`, but if you already have the data in some contiguous memory buffers, then use `std::vector<boost::span<double>>` to avoid the copy.  
  
```cpp  
#include <boost/histogram.hpp>  
#include <vector>  
#include <boost/core/span.hpp>  
  
namespace bh = boost::histogram;  
  
int main() {  
  using dax_t = bh::axis::regular<double>;  
  std::vector<dax_t> axes {  
    dax_t(1, -0.5, 0.5),  
    dax_t(1, -0.5, 0.5)  
  };  
  auto hdyn = bh::make_weighted_histogram(axes);  
  
  // two potentially large arrays, which we don't want to copy  
  std::vector<double> x = {0.0, 1.0, 2.0};  
  std::vector<double> y = {3.0, 4.0, 5.0};  
  
  // converting to required format without copying contents of x and y  
  std::vector<boost::span<double>> xy = {boost::make_span(x), boost::make_span(y)};  
  
  // Alternatively, if you know at compile-time the data are two-dimensional, then do:  
  //     boost::span<double> xy[2] = {boost::make_span(x), boost::make_span(y)};  
  // this avoids a dynamic memory allocation (some stdlib implementations also generate   
  // small vectors on the stack, but that is not guaranteed.  
  // Or use https://www.boost.org/doc/libs/latest/doc/html/container/non_standard_containers.html#container.non_standard_containers.small_vector  
  // which guarantees this optimization.  
  
  // you can also pass a std::vector<double> with bh::weight  
  hdyn.fill(xy, bh::weight(0.5));  
  
  return 0;  
}  
```  
  
- [x] Add this to the guide.
