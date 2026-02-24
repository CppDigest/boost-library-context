# #680 - Unified rasterization API proposal [Closed]

> Username: marco-langer  
> Created at: 2022-06-03 06:20:50 UTC  
> Updated at: 2022-06-27 17:25:45 UTC  
> Closed at: 2022-06-27 14:03:07 UTC  
> Url: https://github.com/boostorg/gil/issues/680  

### Description  
Three different kind of rasterizers have been implemented during GSoC 2020 and GSoC 2021: line, circle and ellipse rasterizers. These implementations are currently [staged as extensions for releasing with Boost 1.80](https://github.com/boostorg/gil/issues/667). While they all serve a similar purpose, their API is considerable different. Some of my concerns I have already summerized [in another comment](https://github.com/boostorg/gil/pull/585#issuecomment-1144412220).  
  
Suppose we want to draw a red circle or ellipse onto a black background image using the current API:  
  
Circle:  
```C++  
#include <boost/gil.hpp>  
#include <boost/gil/extension/io/png.hpp>  
#include <boost/gil/extension/rasterization/circle.hpp>  
  
namespace gil = boost::gil;  
  
int main()  
{  
    using view_t = gil::rgb8_image_t::view_t;  
  
    gil::rgb8_image_t image(256, 256);  
    view_t buffer = gil::view(image);  
  
    gil::rgb8_pixel_t pixel(255, 0, 0);  
    gil::point_t center(100, 100);  
    std::ptrdiff_t radius(50);  
    gil::midpoint_circle_rasterizer rasterizer;  
  
    std::vector<gil::point_t> trajectory(rasterizer.point_count(radius));  
    rasterizer(radius, center, trajectory.begin());  
    for (const gil::point_t& point : trajectory)  
    {  
        buffer(point) = pixel;  
    }  
  
    gil::write_view("circle.png", buffer, gil::png_tag{});  
}  
```  
  
Ellipse:  
```C++  
#include <boost/gil.hpp>  
#include <boost/gil/extension/io/png.hpp>  
#include <boost/gil/extension/rasterization/ellipse.hpp>  
  
namespace gil = boost::gil;  
  
int main()  
{  
    using view_t = gil::rgb8_image_t::view_t;  
  
    gil::rgb8_image_t image(256, 256);  
    view_t buffer = gil::view(image);  
  
    std::vector<unsigned int> pixel{255, 0, 0};  
    std::array<unsigned int, 2> center{100, 100};  
    std::array<unsigned int, 2> semi_axes{50, 30};  
    gil::midpoint_elliptical_rasterizer rasterizer;  
  
    rasterizer(buffer, pixel, center, semi_axes);  
  
    gil::write_view("ellipse.png", buffer, gil::png_tag{});  
}  
```  
  
The circle API only supports generating the curve via an output iterator and puts the burden of applying the pixels to the image on the client-side. The ellipse API applies the pixels via implemented call operator to the image. Both solutions do not fit very well in the overall Boost.Gil API design in my opinion.  
  
### Proposed solution  
One possible solution to unify the API of all currently implemented rasterizers and possible future implementations could be to separate the drawing aspect into a free function:  
  
Circle:  
```C++  
#include <boost/gil.hpp>  
#include <boost/gil/extension/io/png.hpp>  
#include <boost/gil/extension/rasterization/circle_rasterizer.hpp>  
  
namespace gil = boost::gil;  
  
int main()  
{  
    using view_t = gil::rgb8_image_t::view_t;  
  
    gil::rgb8_image_t image(256, 256);  
    view_t buffer = gil::view(image);  
  
    gil::rgb8_pixel_t pixel(255, 0, 0);  
    gil::point_t center{100, 100};  
    std::ptrdiff_t radius(50);  
    gil::midpoint_circle_rasterizer rasterizer(center, radius);  
  
    gil::apply_rasterizer(buffer, rasterizer, pixel);  
  
    gil::write_view("circle.png", buffer, gil::png_tag{});  
}  
```  
  
Ellipse:  
```C++  
#include <boost/gil.hpp>  
#include <boost/gil/extension/io/png.hpp>  
#include <boost/gil/extension/rasterization/ellipse_rasterizer.hpp>  
  
namespace gil = boost::gil;  
  
int main()  
{  
    using view_t = gil::rgb8_image_t::view_t;  
   
    gil::rgb8_image_t image(256, 256);  
    view_t buffer = gil::view(image);  
  
    gil::rgb8_pixel_t pixel(255, 0, 0);  
    gil::point_t center{100, 100};  
    gil::semi_axes_t semi_axes{50, 30};  
    gil::midpoint_ellipse_rasterizer rasterizer(center, semi_axes);  
  
    gil::apply_rasterizer(buffer, rasterizer, pixel);  
  
    gil::write_view("ellipse.png", buffer, gil::png_tag{});  
}  
```  
  
### Possible implementation  
The free function approach could be implemented via tag-dispatch to a detail functor:  
  
apply_rasterizer.hpp:  
```C++  
#ifndef BOOST_GIL_APPLY_RASTERIZER  
#define BOOST_GIL_APPLY_RASTERIZER  
  
namespace boost { namespace gil {  
  
namespace detail {  
  
template <typename View, typename Rasterizer, typename Pixel, typename Tag>  
struct apply_rasterizer_op  
{  
    void operator()(  
        View const& view, Rasterizer const& rasterizer, Pixel const& pixel);  
};  
  
} // namespace detail  
  
template <typename View, typename Rasterizer, typename Pixel>  
void apply_rasterizer(  
    View const& view, Rasterizer const& rasterizer, Pixel const& pixel)  
{  
    using tag_t = typename Rasterizer::type;  
    detail::apply_rasterizer_op<View, Rasterizer, Pixel, tag_t>()(  
        view, rasterizer, pixel);  
}  
  
}} // namespace boost::gil  
  
#endif  
```  
  
circle_rasterizer.hpp:  
```C++  
#ifndef BOOST_GIL_CIRCLE_RASTERIZER  
#define BOOST_GIL_CIRCLE_RASTERIZER  
  
#include <boost/gil/point.hpp>  
#include <boost/gil/apply_rasterizer.hpp>  
  
#include <cstdint>  
#include <iterator>  
#include <vector>  
  
namespace boost { namespace gil {  
  
struct circle_rasterizer_t{};  
  
struct midpoint_circle_rasterizer  
{  
    using type = circle_rasterizer_t;  
  
    auto point_count() const noexcept -> std::size_t  
    {  
       // implementation here  
       return std::size_t{};  
    }  
  
    template <typename OutputIterator>  
    void operator()(OutputIterator d_first) const  
    {  
       // implementation here  
    }  
  
    point_t center;  
    std::ptrdiff_t radius;  
};  
  
namespace detail {  
  
template <typename View, typename Rasterizer, typename Pixel>  
struct apply_rasterizer_op<View, Rasterizer, Pixel, circle_rasterizer_t>  
{  
    void operator()(  
        View const& view, Rasterizer const& rasterizer, Pixel const& pixel)  
    {  
        std::vector<point_t> trajectory;  
        trajectory.reserve(rasterizer.point_count());  
        rasterizer(std::back_inserter(trajectory));  
  
        for (const auto& point : trajectory)  
        {  
            view(point) = pixel;  
        }  
    }  
};  
  
} //namespace detail  
  
}} // namespace boost::gil  
  
#endif  
```

---

## Comment 1

> Username: mloskot  
> Created at: 2022-06-03 06:55:15 UTC  
> Updated at: 2022-06-03 06:56:48 UTC  
> Url: https://github.com/boostorg/gil/issues/680#issuecomment-1145648031  

I like your proposal very much. Both, interfaces and implementation. Such unification via an extra level of indirection does not change the existing, let's say, low level interface, yet offers a clean and friendly one.  
It would be nice to have it for Boost 1.80

---

## Comment 2

> Username: simmplecoder  
> Created at: 2022-06-03 18:24:15 UTC  
> Url: https://github.com/boostorg/gil/issues/680#issuecomment-1146244091  

At the start I thought about providing some sort of fancy iterator that would avoid creating temporary vector. Now with the advent of coroutines I believe it would be great to just create a coroutine that would yield coordinates, then build an easy to use frontend that would support the basic use cases.

---

## Comment 3

> Username: marco-langer  
> Created at: 2022-06-03 18:59:47 UTC  
> Updated at: 2022-06-03 19:57:48 UTC  
> Url: https://github.com/boostorg/gil/issues/680#issuecomment-1146272127  

Yes, I also thought that lazy-evaluated coroutines could eliminate the need for extra memory allocation. This proposal here could serve as a stable API across different kind of rasterizers and the implementation could easily be changed in C++20 without having to change the interface.

---

## Comment 4

> Username: mloskot  
> Created at: 2022-06-03 19:12:22 UTC  
> Url: https://github.com/boostorg/gil/issues/680#issuecomment-1146280641  

I think there is nothing wrong with hosting conditionally-compiled implementation based on coroutines enabled for C++20 compilation.

---

## Comment 5

> Username: marco-langer  
> Created at: 2022-06-27 14:03:07 UTC  
> Updated at: 2022-06-27 17:25:45 UTC  
> Url: https://github.com/boostorg/gil/issues/680#issuecomment-1167393999  

I will close the issue as it is merged.  
  
And regarding coroutines: If P2502 makes it into C++23 (looks promising), my personal preference would be to delay implementation of generators until then rather than reinventing the wheel.

---

## Comment 6

> Username: mloskot  
> Created at: 2022-06-27 17:03:14 UTC  
> Url: https://github.com/boostorg/gil/issues/680#issuecomment-1167616654  

Yes, thanks. I'm so used to issues being closed via commits and I tend to forget to check they have been closed indeed 😊  
  
I agree with your plan.
