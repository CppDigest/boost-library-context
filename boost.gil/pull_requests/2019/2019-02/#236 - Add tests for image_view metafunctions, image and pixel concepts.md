# #236 Add tests for image_view metafunctions, image and pixel concepts [Merged]

> Username: mloskot  
> Created at: 2019-02-17 21:45:31 UTC  
> Updated at: 2019-03-29 20:29:15 UTC  
> Merged at: 2019-02-18 21:52:31 UTC  
> Closed at: 2019-02-18 21:52:31 UTC  
> Url: https://github.com/boostorg/gil/pull/236  

Rename test source files `xxx_concepts.cpp` to `concepts.cpp`  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
-----  
  
~~I expect some of the NEW tests may fail due to `incomplete type`, especially with GCC or clang, and this should indicate a bug.~~  
  
Most of the new tests include `#include <boost/gil.hpp>` instead of including headers selectively. I left explanation in `image_view/concepts.cpp` test:  
  
```cpp  
// FIXME: There are missing headers internally, leading to incomplete types  
#if 0  
#include <boost/gil/image_view.hpp>  
#include <boost/gil/planar_pixel_reference.hpp>  
#include <boost/gil/typedefs.hpp>  
#else  
#include <boost/gil.hpp>  
#endif  
```   
  
There is so many forward-declared types (e.g. see `metafunctions.hpp`), also to avoid circular dependencies between headers (e.g. `planar_pixel_iterator.hpp` and `planar_pixel_reference.hpp`), that it is very difficult to track what is actually necessary to include. This easily leads to compilation errors due to incomplete types (esp. with GCC and clang):  
  
```  
In file included from /mnt/d/boost.wsl/libs/gil/test/image_view/derived_view_type.cpp:8:  
In file included from /mnt/d/boost.wsl/libs/gil/include/boost/gil/concepts.hpp:13:  
In file included from /mnt/d/boost.wsl/libs/gil/include/boost/gil/concepts/channel.hpp:11:  
In file included from /mnt/d/boost.wsl/libs/gil/include/boost/gil/concepts/basic.hpp:25:  
In file included from /mnt/d/boost.wsl/libs/gil/include/boost/gil/concepts/concept_check.hpp:23:  
In file included from /mnt/d/boost.wsl/boost/concept_check.hpp:23:  
In file included from /mnt/d/boost.wsl/boost/type_traits/conversion_traits.hpp:15:  
/mnt/d/boost.wsl/boost/type_traits/is_convertible.hpp:498:4: error: static_assert failed "From argument type to is_convertible must be a complete type"  
   BOOST_STATIC_ASSERT_MSG(boost::is_complete<From>::value || boost::is_void<From>::value || boost::is_array<From>::value, "From argument type to is_convertible must be a complete type");  
   ^                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/mnt/d/boost.wsl/boost/static_assert.hpp:31:45: note: expanded from macro 'BOOST_STATIC_ASSERT_MSG'  
#     define BOOST_STATIC_ASSERT_MSG( ... ) static_assert(__VA_ARGS__)  
                                            ^             ~~~~~~~~~~~  
/mnt/d/boost.wsl/boost/mpl/aux_/nested_type_wknd.hpp:27:7: note: in instantiation of template class 'boost::is_convertible<const boost::gil::planar_pixel_reference<const unsigned char &, boost::mpl::vector4<boost::gil::cyan_t, boost::gil::magenta_t, boost::gil::yellow_t, boost::gil::black_t> >, boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector4<boost::gil::cyan_t, boost::gil::magenta_t, boost::gil::yellow_t, boost::gil::black_t>, boost::mpl::range_c<int, 0, 4> > > >' requested here  
    : T::type  
      ^  
```  
  
This is quite  a mess and it should be cleaned up, headers like `metafunctions.hpp` should be split into smaller, etc. What do you think about splitting into smaller headers, @stefanseefeld ?

---
