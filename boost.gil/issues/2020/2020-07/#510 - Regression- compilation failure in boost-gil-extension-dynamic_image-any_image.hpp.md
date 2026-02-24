# #510 - Regression: compilation failure in boost/gil/extension/dynamic_image/any_image.hpp [Closed]

> Username: apolukhin  
> Created at: 2020-07-22 17:35:21 UTC  
> Updated at: 2020-07-27 15:33:50 UTC  
> Closed at: 2020-07-27 15:33:50 UTC  
> Url: https://github.com/boostorg/gil/issues/510  

### Actual behavior  
  
Code that was working fine in Boost. 1.73 now fails to compile:  
  
```  
antoshkka@antishkka-amd:/data/Boost-Cookbook-4880OS/Chapter12/07_gil$ g++-9 main.cpp -I /data/boost/  
In file included from /data/boost/boost/gil/extension/dynamic_image/algorithm.hpp:11,  
                 from /data/boost/boost/gil/extension/dynamic_image/dynamic_image_all.hpp:11,  
                 from /data/boost/boost/gil/extension/toolbox/dynamic_images.hpp:11,  
                 from /data/boost/boost/gil/extension/toolbox/toolbox.hpp:13,  
                 from /data/boost/boost/gil/io/base.hpp:11,  
                 from /data/boost/boost/gil/extension/io/png/tags.hpp:11,  
                 from /data/boost/boost/gil/extension/io/png/read.hpp:13,  
                 from /data/boost/boost/gil/extension/io/png.hpp:11,  
                 from /data/boost/boost/gil/extension/io/png/old.hpp:11,  
                 from main.cpp:17:  
/data/boost/boost/gil/extension/dynamic_image/any_image.hpp: In substitution of ‘template<class T> using get_view_t = typename T::view_t [with T = boost::mpl::vector3<boost::gil::image<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mp11::mp_list<boost::gil::gray_color_t> > >, false, std::allocator<unsigned char> >, boost::gil::image<boost::gil::pixel<short unsigned int, boost::gil::layout<boost::mp11::mp_list<boost::gil::gray_color_t> > >, false, std::allocator<unsigned char> >, boost::gil::image<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mp11::mp_list<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > >, false, std::allocator<unsigned char> > >]’:  
/data/boost/boost/mp11/algorithm.hpp:52:11:   required from ‘struct boost::mp11::detail::mp_transform_impl<boost::gil::detail::get_view_t, boost::gil::any_image<boost::mpl::vector3<boost::gil::image<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mp11::mp_list<boost::gil::gray_color_t>, boost::mp11::mp_list<std::integral_constant<int, 0> > > >, false, std::allocator<unsigned char> >, boost::gil::image<boost::gil::pixel<short unsigned int, boost::gil::layout<boost::mp11::mp_list<boost::gil::gray_color_t>, boost::mp11::mp_list<std::integral_constant<int, 0> > > >, false, std::allocator<unsigned char> >, boost::gil::image<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mp11::mp_list<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mp11::mp_list<std::integral_constant<int, 0>, std::integral_constant<int, 1>, std::integral_constant<int, 2> > > >, false, std::allocator<unsigned char> > > > >’  
/data/boost/boost/gil/extension/dynamic_image/any_image.hpp:93:11:   required from ‘class boost::gil::any_image<boost::mpl::vector3<boost::gil::image<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mp11::mp_list<boost::gil::gray_color_t>, boost::mp11::mp_list<std::integral_constant<int, 0> > > >, false, std::allocator<unsigned char> >, boost::gil::image<boost::gil::pixel<short unsigned int, boost::gil::layout<boost::mp11::mp_list<boost::gil::gray_color_t>, boost::mp11::mp_list<std::integral_constant<int, 0> > > >, false, std::allocator<unsigned char> >, boost::gil::image<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mp11::mp_list<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mp11::mp_list<std::integral_constant<int, 0>, std::integral_constant<int, 1>, std::integral_constant<int, 2> > > >, false, std::allocator<unsigned char> > > >’  
main.cpp:64:38:   required from here  
/data/boost/boost/gil/extension/dynamic_image/any_image.hpp:31:7: error: no type named ‘view_t’ in ‘struct boost::mpl::vector3<boost::gil::image<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mp11::mp_list<boost::gil::gray_color_t> > >, false, std::allocator<unsigned char> >, boost::gil::image<boost::gil::pixel<short unsigned int, boost::gil::layout<boost::mp11::mp_list<boost::gil::gray_color_t> > >, false, std::allocator<unsigned char> >, boost::gil::image<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mp11::mp_list<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > >, false, std::allocator<unsigned char> > >’  
   31 | using get_view_t = typename T::view_t;  
      |       ^~~~~~~~~~  
  
```  
  
  
### Expected  behavior  
  
Code compiles and runs  
  
### C++ Minimal Working Example  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
```cpp  
#include <png.h>  
  
// MinGW workarounds for https://svn.boost.org/trac10/ticket/3908 :  
#ifndef png_infopp_NULL  
#   define png_infopp_NULL NULL  
#endif  
#ifndef int_p_NULL  
#   define int_p_NULL NULL  
#endif  
  
#include <boost/version.hpp>  
#if (BOOST_VERSION < 106800)  
#   include <boost/gil/gil_all.hpp>  
#   include <boost/gil/extension/io/png_dynamic_io.hpp>  
#else  
#   include <boost/gil.hpp>  
#   include <boost/gil/extension/io/png/old.hpp>  
#endif  
#include <boost/mpl/vector.hpp>  
  
#include <string>  
  
#include <cassert>  
  
struct negate {  
    typedef void result_type; // required  
  
    template <class View>  
    void operator()(const View& source) const {  
  
// Code formatting is shifted left to fit page width  
boost::gil::gil_function_requires<  
    boost::gil::ImageViewConcept<View>  
>();  
  
typedef typename View::value_type value_type;  
typedef typename boost::gil::channel_type<value_type>::type channel_t;  
  
const std::size_t channels = boost::gil::num_channels<View>::value;  
const channel_t max_val = (std::numeric_limits<channel_t>::max)();  
  
for (unsigned int y = 0; y < source.height(); ++y) {  
    for (unsigned int x = 0; x < source.width(); ++x) {  
        for (unsigned int c = 0; c < channels; ++c) {  
            source(x, y)[c] = max_val - source(x, y)[c];  
        }  
    }  
}  
  
  
    }  
}; // negate  
  
int main(int argc, char *argv[]) {  
    assert(argc == 2);  
  
    typedef boost::mpl::vector3<  
            boost::gil::gray8_image_t,  
            boost::gil::gray16_image_t,  
            boost::gil::rgb8_image_t  
    > img_types;  
  
    std::string file_name(argv[1]);  
    boost::gil::any_image<img_types> source;  
    boost::gil::png_read_image(file_name, source);  
  
    boost::gil::apply_operation(  
        view(source),  
        negate()  
    );  
  
    boost::gil::png_write_view("negate_" + file_name, const_view(source));  
}  
  
```  
  
Taken from here: https://github.com/apolukhin/Boost-Cookbook/blob/second_edition/Chapter12/07_gil/main.cpp  
  
### Environment  
  
- Build settings: See all of them at https://travis-ci.org/github/apolukhin/Boost-Cookbook/builds/708924611  
- Version (Git ref or `<boost/version.hpp>`): develop version of Boost (1.74 beta is probably also affected)

---

## Comment 1

> Username: sdebionne  
> Created at: 2020-07-24 13:05:06 UTC  
> Updated at: 2020-07-24 13:05:16 UTC  
> Url: https://github.com/boostorg/gil/issues/510#issuecomment-663528239  

It's a breaking change, admittedly not very well documented.  
  
```c++  
boost::gil::any_image<  
    boost::gil::gray8_image_t,  
    boost::gil::gray16_image_t,  
    boost::gil::rgb8_image_t> source;  
```  
  
should work. No need for a MPL Sequence anymore since we have variadic template in C++11 (required for GIL since 1.68).

---

## Comment 2

> Username: mloskot  
> Created at: 2020-07-24 13:43:06 UTC  
> Updated at: 2020-07-24 17:44:25 UTC  
> Url: https://github.com/boostorg/gil/issues/510#issuecomment-663545245  

Basically, as part of [C++11 modernisation](https://github.com/boostorg/gil/projects/3) started in Boost 1.68, we've removed uses of MPL (replacing them with MP11) in Boost 1.72.  
Unfortunately, the changes in Boost 1.72 did not contain complete removal of (indirect) dependency on MPL (e.g. via Boost.Variant, etc.)  
In Boost 1.74, thanks to @sdebionne contributions, we have completed the removal of, hopefully, any uses, direct or indirect, of  MPL.  
  
-----  
  
I have updated our release notes to make the change clearer https://github.com/boostorg/gil/commit/f3ba17fc9e4a7bbacf7daf01db9e86f9005e9ee8  
  
  
-----  
  
I did not know/or forgot about the Boost Cookbook. I think it is important piece of material and to keep it up to date. I will try to include it in my testing of GIL. Thanks @sdebionne for updating it.

---

## Comment 3

> Username: apolukhin  
> Created at: 2020-07-24 17:28:35 UTC  
> Url: https://github.com/boostorg/gil/issues/510#issuecomment-663648397  

> It's a breaking change, admittedly not very well documented.  
>   
> ```c++  
> boost::gil::any_image<  
>     boost::gil::gray8_image_t,  
>     boost::gil::gray16_image_t,  
>     boost::gil::rgb8_image_t> source;  
> ```  
>   
> should work. No need for a MPL Sequence anymore since we have variadic template in C++11 (required for GIL since 1.68).  
  
I've tried that and now GIL fails to substitute the apply_visitor: https://travis-ci.org/github/apolukhin/Boost-Cookbook/jobs/711455496#L2754

---

## Comment 4

> Username: mloskot  
> Created at: 2020-07-26 21:41:09 UTC  
> Url: https://github.com/boostorg/gil/issues/510#issuecomment-664044111  

I think, I see the problem:  
  
1. Although CI jobs passed for PR #491, that change was not verified by any of the C++11 mode builds.  
   It, possibly, was only verified by VS builds which are C++14.  
   That is due to, still, lack of proper coverages of `dynamic_image` with tests.  
  
2. The `Visitor` argument of `apply_operation` changed from by-value to `&&`  
  
3. Some of `Visitor`-s are passed by l-value reference, then in C++11 mode this `typename Visitor::result_type` simply fails and such `apply_operation` candidate template is simply ignored.  
  
A dumb fix is  
  
```cpp  
 auto apply_operation(Variant1&& arg1, Visitor&& op)  
 #if defined(BOOST_NO_CXX14_DECLTYPE_AUTO) || defined(BOOST_NO_CXX11_DECLTYPE_N3276)  
-auto apply_operation(Variant1&& arg1, Visitor&& op)  
+auto apply_operation(Variant1&& arg1, Visitor op)  
```  
  
or a less dumb  
  
```cpp  
 auto apply_operation(Variant1&& arg1, Visitor&& op)  
 #if defined(BOOST_NO_CXX14_DECLTYPE_AUTO) || defined(BOOST_NO_CXX11_DECLTYPE_N3276)  
-    -> typename Visitor::result_type  
+    -> typename std::remove_reference<Visitor>::type::result_type  
```  
  
@sdebionne What do you think?

---

## Comment 5

> Username: sdebionne  
> Created at: 2020-07-27 09:23:23 UTC  
> Url: https://github.com/boostorg/gil/issues/510#issuecomment-664234137  

@mloskot Thank you for investigating this! It passed through the cracks unnoticed on my side too (std > c++11).  
  
1. `dynamic_image` needs better test coverage for sure, I'll need to find some time to work on that.  
  
2. Correct, it sounded sensible to use the same signature than the underlying variant.  
  
3. I looked quickly at Boost.Variant2 implementation and given the complexity of the `Vret<>` metafunction, I would be tempted to use it:  
  
```c++  
 auto apply_operation(Variant1&& arg1, Visitor&& op)  
 #if defined(BOOST_NO_CXX14_DECLTYPE_AUTO) || defined(BOOST_NO_CXX11_DECLTYPE_N3276)  
-    -> typename Visitor::result_type  
+    -> variant2::detail::Vret<Visitor, Variant1>  
```  
  
The problem is the usage of code in the `detail` namespace. Maybe @pdimov could comment on that?

---

## Comment 6

> Username: pdimov  
> Created at: 2020-07-27 12:15:26 UTC  
> Url: https://github.com/boostorg/gil/issues/510#issuecomment-664360194  

Use decltype, it's C++11: `-> decltype(variant2::visit(std::forward<Visitor>(op), std::forward<Variant1>(arg1)))`

---

## Comment 7

> Username: mloskot  
> Created at: 2020-07-27 15:08:47 UTC  
> Url: https://github.com/boostorg/gil/issues/510#issuecomment-664454176  

I knew there must be a clever way ;) Thanks @pdimov
