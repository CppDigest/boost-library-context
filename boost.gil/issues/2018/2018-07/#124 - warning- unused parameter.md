# #124 - warning: unused parameter [Closed]

> Username: kivadiu  
> Created at: 2018-07-30 06:51:17 UTC  
> Updated at: 2019-01-11 22:46:18 UTC  
> Closed at: 2019-01-11 22:46:18 UTC  
> Url: https://github.com/boostorg/gil/issues/124  

### Minimal Working Example (in C++)  
When I compile this small program  
```  
#include <boost/gil/extension/io/jpeg.hpp>  
int main() {  
  return 0;  
}  
```  
with clang 5.0.2 on linux x86_64  
`clang++ -o gil.o -c -O2 -Wall -Wextra -std=c++14 -I/softs/lin64-clang-5.0.2/release/boost/include gil.cpp`  
### Actual behavior  
I get the following warnings:  
```  
In file included from gil.cpp:1:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/io/jpeg.hpp:22:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/io/jpeg/read.hpp:24:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/io/jpeg/tags.hpp:45:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/io/base.hpp:34:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/toolbox/toolbox.hpp:24:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/toolbox/image_types.hpp:22:  
/softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/toolbox/image_types/indexed_image.hpp:190:5: warning: 'const' type qualifier on return type has no effect [-Wignored-qualifiers]  
    const std::size_t num_colors() const { return _num_colors; }  
    ^~~~~~  
In file included from gil.cpp:1:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/io/jpeg.hpp:22:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/io/jpeg/read.hpp:24:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/io/jpeg/tags.hpp:45:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/io/base.hpp:34:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/toolbox/toolbox.hpp:25:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/toolbox/metafunctions.hpp:22:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/toolbox/metafunctions/channel_type.hpp:29:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/toolbox/dynamic_images.hpp:27:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/dynamic_image/dynamic_image_all.hpp:26:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/dynamic_image/algorithm.hpp:16:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/dynamic_image/any_image.hpp:24:  
/softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/dynamic_image/any_image_view.hpp:41:63: warning: unused parameter 'v' [-Wunused-parameter]  
        template <typename T> result_type operator()(const T& v) const { return num_channels<T>::value; }  
                                                              ^  
In file included from gil.cpp:1:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/io/jpeg.hpp:22:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/io/jpeg/read.hpp:24:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/io/jpeg/tags.hpp:45:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/io/base.hpp:34:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/toolbox/toolbox.hpp:25:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/toolbox/metafunctions.hpp:22:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/toolbox/metafunctions/channel_type.hpp:29:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/toolbox/dynamic_images.hpp:27:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/dynamic_image/dynamic_image_all.hpp:26:  
/softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/dynamic_image/algorithm.hpp:147:70: warning: unused parameter 'src' [-Wunused-parameter]  
    template <typename V, typename Value> static void apply(const V& src, const Value& val) { throw std::bad_cast();}  
                                                                     ^  
/softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/dynamic_image/algorithm.hpp:147:88: warning: unused parameter 'val' [-Wunused-parameter]  
    template <typename V, typename Value> static void apply(const V& src, const Value& val) { throw std::bad_cast();}  
                                                                                       ^  
In file included from gil.cpp:1:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/io/jpeg.hpp:22:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/io/jpeg/read.hpp:24:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/io/jpeg/tags.hpp:45:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/io/base.hpp:34:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/toolbox/toolbox.hpp:25:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/toolbox/metafunctions.hpp:22:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/toolbox/metafunctions/channel_type.hpp:29:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/toolbox/dynamic_images.hpp:27:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/dynamic_image/dynamic_image_all.hpp:30:  
/softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/dynamic_image/image_view_factory.hpp:178:140: warning: unused parameter 'cc' [-Wunused-parameter]  
typename color_converted_view_type<any_image_view<ViewTypes>, DstP, CC>::type color_converted_view(const any_image_view<ViewTypes>& src,CC cc) {   
                                                                                                                                           ^  
/softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/dynamic_image/image_view_factory.hpp:201:144: warning: unused parameter 'cc' [-Wunused-parameter]  
typename color_converted_view_type<any_image_view<ViewTypes>, DstP, CC>::type any_color_converted_view(const any_image_view<ViewTypes>& src,CC cc) {   
                                                                                                                                               ^  
In file included from gil.cpp:1:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/io/jpeg.hpp:22:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/io/jpeg/read.hpp:26:  
In file included from /softs/lin64-clang-5.0.2/release/boost/include/boost/gil/extension/io/jpeg/detail/read.hpp:29:  
/softs/lin64-clang-5.0.2/release/boost/include/boost/gil/io/device.hpp:446:31: warning: unused parameter 'data' [-Wunused-parameter]  
    void write( const byte_t* data  
                              ^  
/softs/lin64-clang-5.0.2/release/boost/include/boost/gil/io/device.hpp:447:31: warning: unused parameter 'count' [-Wunused-parameter]  
              , std::size_t   count )  
                              ^  
/softs/lin64-clang-5.0.2/release/boost/include/boost/gil/io/device.hpp:471:35: warning: unused parameter 'data' [-Wunused-parameter]  
    std::size_t read( byte_t*     data  
                                  ^  
/softs/lin64-clang-5.0.2/release/boost/include/boost/gil/io/device.hpp:472:35: warning: unused parameter 'count' [-Wunused-parameter]  
                    , std::size_t count )  
                                  ^  
10 warnings generated.  
```  
### Expected  behavior  
no warnings.  
### Environment  
  
All relevant information like:  
  
- Boost version (see `<boost/version.hpp>`): 1.68.0 beta1  
- Compiler version: clang++ 5.0.2  
- Build settings: -O2 -Wall -Wextra -std=c++14

---

## Comment 1

> Username: mloskot  
> Created at: 2018-07-30 07:50:43 UTC  
> Url: https://github.com/boostorg/gil/issues/124#issuecomment-408777537  

Although I agree we should (and will!) aim for warning-less compilation, those warnings are benign and I don't think it should be priority to fix them all during the late phase of Boost 1.68 releasing.  
  
Since you've already done some warnings cleaning, @stefanseefeld, I leave the decision to you.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2018-07-30 07:54:40 UTC  
> Url: https://github.com/boostorg/gil/issues/124#issuecomment-408778489  

@kivadiu , the warnings you report look exactly like the warnings I already fixed in https://github.com/boostorg/gil/pull/123. Can you please re-run your test on the current `develop` branch ? I intend to merge that into `master` sometime today or tomorrow...  
Thanks,

---

## Comment 3

> Username: kivadiu  
> Created at: 2018-07-30 09:00:34 UTC  
> Url: https://github.com/boostorg/gil/issues/124#issuecomment-408795796  

I saw your commit and it seems that it addresses my concerns. Thank you.  
I am not sure how I can test directly from develop because I use to use tarballs of the whole boost. Maybe I can just untar in 1.68.0 beta 1 and retest or just apply the patch. I will see. Thanks again.

---

## Comment 4

> Username: kivadiu  
> Created at: 2018-07-30 09:08:24 UTC  
> Url: https://github.com/boostorg/gil/issues/124#issuecomment-408797901  

OK I untared the develp branch in 1.68.0 beta and it fixed nearly all warnings. The only one I now get is this one:  
```  
boost/gil/io/device.hpp:319:21: warning: unused variable 'num_elements' [-Wunused-variable]  
        std::size_t num_elements = fwrite( line.c_str()  
                    ^  
```  
Because the variable is used only in debug mode.  
Thanks again

---

## Comment 5

> Username: mloskot  
> Created at: 2019-01-11 22:46:18 UTC  
> Url: https://github.com/boostorg/gil/issues/124#issuecomment-453681250  

> warning: unused variable 'num_elements'   
  
This was fixed in https://github.com/boostorg/gil/commit/871b7b6897dad9d0d0e1e88d7679700bba645838 and released in Boost 1.69.
