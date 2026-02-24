# #183 - Conflicting definitions in io/dynamic_io_new.hpp and extension/toolbox/dynamic_images.hpp [Closed]

> Username: mloskot  
> Created at: 2018-12-11 19:35:23 UTC  
> Updated at: 2018-12-12 17:02:11 UTC  
> Closed at: 2018-12-12 16:58:58 UTC  
> Url: https://github.com/boostorg/gil/issues/183  

Including the two headers in single TU prevents compilation due to redefinitions of   
  
```  
any_image_channel_t  
any_image_color_space_t  
any_image_pixel_t  
```  
  
and possibly more.  
  
### Minimal Working Example (in C++)  
  
```  
#include <boost/gil/io/dynamic_io_new.hpp>  
#include <boost/gil/extension/toolbox/dynamic_images.hpp>  
int main() { return 0; }  
```  
  
### Actual behavior  
  
```  
$ g++ -std=c++11 -I/mnt/d/boost.wsl test.cpp  
In file included from test.cpp:2:0:  
/mnt/d/boost.wsl/boost/gil/extension/toolbox/dynamic_images.hpp:19:8: error: redefinition of ‘struct boost::gil::any_image_pixel_t’  
 struct any_image_pixel_t       {};  
        ^  
In file included from test.cpp:1:0:  
/mnt/d/boost.wsl/boost/gil/io/dynamic_io_new.hpp:21:8: error: previous definition of ‘struct boost::gil::any_image_pixel_t’  
 struct any_image_pixel_t       {};  
        ^  
In file included from test.cpp:2:0:  
/mnt/d/boost.wsl/boost/gil/extension/toolbox/dynamic_images.hpp:20:8: error: redefinition of ‘struct boost::gil::any_image_channel_t’  
 struct any_image_channel_t     {};  
        ^  
In file included from test.cpp:1:0:  
/mnt/d/boost.wsl/boost/gil/io/dynamic_io_new.hpp:22:8: error: previous definition of ‘struct boost::gil::any_image_channel_t’  
 struct any_image_channel_t     {};  
        ^  
In file included from test.cpp:2:0:  
/mnt/d/boost.wsl/boost/gil/extension/toolbox/dynamic_images.hpp:21:8: error: redefinition of ‘struct boost::gil::any_image_color_space_t’  
 struct any_image_color_space_t {};  
        ^  
In file included from test.cpp:1:0:  
/mnt/d/boost.wsl/boost/gil/io/dynamic_io_new.hpp:23:8: error: previous definition of ‘struct boost::gil::any_image_color_space_t’  
 struct any_image_color_space_t {};  
        ^  
In file included from test.cpp:2:0:  
/mnt/d/boost.wsl/boost/gil/extension/toolbox/dynamic_images.hpp:26:8: error: redefinition of ‘struct boost::gil::detail::construct_matched_t<N>’  
 struct construct_matched_t {  
        ^  
In file included from test.cpp:1:0:  
/mnt/d/boost.wsl/boost/gil/io/dynamic_io_new.hpp:28:8: error: previous definition of ‘struct boost::gil::detail::construct_matched_t<N>’  
 struct construct_matched_t {  
        ^  
In file included from test.cpp:2:0:  
/mnt/d/boost.wsl/boost/gil/extension/toolbox/dynamic_images.hpp:37:8: error: redefinition of ‘struct boost::gil::detail::construct_matched_t<0l>’  
 struct construct_matched_t<0> {  
        ^  
In file included from test.cpp:1:0:  
/mnt/d/boost.wsl/boost/gil/io/dynamic_io_new.hpp:39:8: error: previous definition of ‘struct boost::gil::detail::construct_matched_t<0l>’  
 struct construct_matched_t<0> {  
        ^  
In file included from test.cpp:2:0:  
/mnt/d/boost.wsl/boost/gil/extension/toolbox/dynamic_images.hpp:46:7: error: redefinition of ‘class boost::gil::detail::dynamic_io_fnobj<IsSupported, OpClass>’  
 class dynamic_io_fnobj {  
       ^  
In file included from test.cpp:1:0:  
/mnt/d/boost.wsl/boost/gil/io/dynamic_io_new.hpp:48:7: error: previous definition of ‘class boost::gil::detail::dynamic_io_fnobj<IsSupported, OpClass>’  
 class dynamic_io_fnobj {  
       ^  
In file included from test.cpp:2:0:  
/mnt/d/boost.wsl/boost/gil/extension/toolbox/dynamic_images.hpp:100:13: error: redefinition of ‘template<class Images, class Pred> bool boost::gil::construct_matched(boost::gil::any_image<Types>&, Pred)’  
 inline bool construct_matched(any_image<Images>& im,Pred pred) {  
             ^  
In file included from test.cpp:1:0:  
/mnt/d/boost.wsl/boost/gil/io/dynamic_io_new.hpp:99:13: note: ‘template<class Images, class Pred> bool boost::gil::construct_matched(boost::gil::any_image<Types>&, Pred)’ previously declared here  
 inline bool construct_matched(any_image<Images>& im,Pred pred) {  
             ^  
```  
  
### Expected  behavior  
  
Successful compilation.
