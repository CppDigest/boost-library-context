# #235 - Concept checking fail for indexed_image [Open]

> Username: mloskot  
> Created at: 2019-02-07 08:56:26 UTC  
> Updated at: 2019-11-07 10:00:58 UTC  
> Url: https://github.com/boostorg/gil/issues/235  

### Minimal Working Example (in C++)  
  
```  
cd libs/gil  
b2 define=BOOST_GIL_USE_CONCEPT_CHECK toolbox/test  
```  
  
### Actual behavior  
  
```  
gcc.compile.c++ ../../bin.v2/libs/gil/toolbox/test/test.test/gcc-5.5.0/debug/threading-multi/visibility-hidden/indexed_image_test.o  
In file included from ../../boost/gil/concepts/image_view.hpp:16:0,  
                 from ../../boost/gil/concepts/image.hpp:14,  
                 from ../../boost/gil/concepts.hpp:17,  
                 from ../../boost/gil/color_base_algorithm.hpp:11,  
                 from ../../boost/gil/color_base.hpp:12,  
                 from ../../boost/gil/pixel.hpp:12,  
                 from ../../boost/gil/bit_aligned_pixel_reference.hpp:11,  
                 from ../../boost/gil/bit_aligned_pixel_iterator.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:11,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/concepts/pixel_iterator.hpp: In instantiation of ‘void boost::gil::detail::RandomAccessIteratorIsMemoryBasedConcept<Iterator>::constraints() [with Iterator = unsigned char*]’:  
../../boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of ‘template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model:: constraints)>*) [with Model = boost::gil::detail::RandomAccessIteratorIsMemoryBasedConcept<unsigned char*>]’  
../../boost/concept/detail/has_constraints.hpp:42:5:   required from ‘const bool boost::concepts::not_satisfied<boost::gil::detail::RandomAccessIteratorIsMemoryBasedConcept<unsigned char*> >::value’  
../../boost/concept/detail/has_constraints.hpp:45:51:   required from ‘struct boost::concepts::not_satisfied<boost::gil::detail::RandomAccessIteratorIsMemoryBasedConcept<unsigned char*> >’  
../../boost/concept/detail/general.hpp:51:8:   required from ‘struct boost::concepts::requirement_<void (*)(boost::gil::detail::RandomAccessIteratorIsMemoryBasedConcept<unsigned char*>)>’  
../../boost/concept_check.hpp:50:7:   required from ‘void boost::function_requires(Model*) [with Model = boost::gil::detail::RandomAccessIteratorIsMemoryBasedConcept<unsigned char*>]’  
../../boost/gil/concepts/concept_check.hpp:45:62:   [ skipping 13 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
../../boost/concept/detail/general.hpp:51:8:   required from ‘struct boost::concepts::requirement_<void (*)(boost::gil::StepIteratorConcept<boost::gil::memory_based_step_iterator<unsigned char*> >)>’  
../../boost/gil/locator.hpp:229:5:   required from ‘class boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<unsigned char*> >’  
../../boost/gil/image_view.hpp:55:48:   required from ‘class boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<unsigned char*> > >’  
../../boost/gil/image.hpp:47:50:   required from ‘class boost::gil::image<unsigned char, false, std::allocator<unsigned char> >’  
../../boost/gil/extension/toolbox/image_types/indexed_image.hpp:245:54:   required from ‘class boost::gil::indexed_image<unsigned char, boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > > >’  
toolbox/test/indexed_image_test.cpp:30:62:   required from here  
../../boost/gil/concepts/pixel_iterator.hpp:94:41: error: ‘memunit_step’ was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]  
         std::ptrdiff_t bs = memunit_step(it);  
                                         ^  
In file included from ../../boost/gil/rgb.hpp:12:0,  
                 from ../../boost/gil/typedefs.hpp:16,  
                 from ../../boost/gil/channel_algorithm.hpp:13,  
                 from ../../boost/gil/color_convert.hpp:11,  
                 from ../../boost/gil/image_view_factory.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:15,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/planar_pixel_iterator.hpp:167:23: note: ‘template<class IC, class C> std::ptrdiff_t boost::gil::memunit_step(const boost::gil::planar_pixel_iterator<IC, C>&)’ declared here, later in the translation unit  
 inline std::ptrdiff_t memunit_step(const planar_pixel_iterator<IC,C>&) { return sizeof(typename std::iterator_traits<IC>::value_type); }  
                       ^  
In file included from ../../boost/gil/concepts/image_view.hpp:16:0,  
                 from ../../boost/gil/concepts/image.hpp:14,  
                 from ../../boost/gil/concepts.hpp:17,  
                 from ../../boost/gil/color_base_algorithm.hpp:11,  
                 from ../../boost/gil/color_base.hpp:12,  
                 from ../../boost/gil/pixel.hpp:12,  
                 from ../../boost/gil/bit_aligned_pixel_reference.hpp:11,  
                 from ../../boost/gil/bit_aligned_pixel_iterator.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:11,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/concepts/pixel_iterator.hpp:97:30: error: ‘memunit_advanced’ was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]  
         it = memunit_advanced(it, 3);  
                              ^  
In file included from ../../boost/gil/rgb.hpp:12:0,  
                 from ../../boost/gil/typedefs.hpp:16,  
                 from ../../boost/gil/channel_algorithm.hpp:13,  
                 from ../../boost/gil/color_convert.hpp:11,  
                 from ../../boost/gil/image_view_factory.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:15,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/planar_pixel_iterator.hpp:188:36: note: ‘template<class IC, class C> boost::gil::planar_pixel_iterator<IC, C> boost::gil::memunit_advanced(const boost::gil::planar_pixel_iterator<IC, C>&, std::ptrdiff_t)’ declared here, later in the translation unit  
 inline planar_pixel_iterator<IC,C> memunit_advanced(const planar_pixel_iterator<IC,C>& p, std::ptrdiff_t diff) {  
                                    ^  
In file included from ../../boost/gil/concepts/image_view.hpp:16:0,  
                 from ../../boost/gil/concepts/image.hpp:14,  
                 from ../../boost/gil/concepts.hpp:17,  
                 from ../../boost/gil/color_base_algorithm.hpp:11,  
                 from ../../boost/gil/color_base.hpp:12,  
                 from ../../boost/gil/pixel.hpp:12,  
                 from ../../boost/gil/bit_aligned_pixel_reference.hpp:11,  
                 from ../../boost/gil/bit_aligned_pixel_iterator.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:11,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/concepts/pixel_iterator.hpp:98:45: error: ‘memunit_distance’ was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]  
         std::ptrdiff_t bd = memunit_distance(it, it);  
                                             ^  
In file included from ../../boost/gil/rgb.hpp:12:0,  
                 from ../../boost/gil/typedefs.hpp:16,  
                 from ../../boost/gil/channel_algorithm.hpp:13,  
                 from ../../boost/gil/color_convert.hpp:11,  
                 from ../../boost/gil/image_view_factory.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:15,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/planar_pixel_iterator.hpp:170:23: note: ‘template<class IC, class C> std::ptrdiff_t boost::gil::memunit_distance(const boost::gil::planar_pixel_iterator<IC, C>&, const boost::gil::planar_pixel_iterator<IC, C>&)’ declared here, later in the translation unit  
 inline std::ptrdiff_t memunit_distance(const planar_pixel_iterator<IC,C>& p1, const planar_pixel_iterator<IC,C>& p2) {  
                       ^  
In file included from ../../boost/gil/concepts/image_view.hpp:16:0,  
                 from ../../boost/gil/concepts/image.hpp:14,  
                 from ../../boost/gil/concepts.hpp:17,  
                 from ../../boost/gil/color_base_algorithm.hpp:11,  
                 from ../../boost/gil/color_base.hpp:12,  
                 from ../../boost/gil/pixel.hpp:12,  
                 from ../../boost/gil/bit_aligned_pixel_reference.hpp:11,  
                 from ../../boost/gil/bit_aligned_pixel_iterator.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:11,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/concepts/pixel_iterator.hpp:101:24: error: ‘memunit_advance’ was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]  
         memunit_advance(it,3);  
                        ^  
In file included from ../../boost/gil/rgb.hpp:12:0,  
                 from ../../boost/gil/typedefs.hpp:16,  
                 from ../../boost/gil/channel_algorithm.hpp:13,  
                 from ../../boost/gil/color_convert.hpp:11,  
                 from ../../boost/gil/image_view_factory.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:15,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/planar_pixel_iterator.hpp:183:13: note: ‘template<class IC, class C> void boost::gil::memunit_advance(boost::gil::planar_pixel_iterator<IC, C>&, std::ptrdiff_t)’ declared here, later in the translation unit  
 inline void memunit_advance(planar_pixel_iterator<IC,C>& p, std::ptrdiff_t diff) {  
             ^  
In file included from ../../boost/gil/concepts/image_view.hpp:16:0,  
                 from ../../boost/gil/concepts/image.hpp:14,  
                 from ../../boost/gil/concepts.hpp:17,  
                 from ../../boost/gil/color_base_algorithm.hpp:11,  
                 from ../../boost/gil/color_base.hpp:12,  
                 from ../../boost/gil/pixel.hpp:12,  
                 from ../../boost/gil/bit_aligned_pixel_reference.hpp:11,  
                 from ../../boost/gil/bit_aligned_pixel_iterator.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:11,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/concepts/pixel_iterator.hpp: In instantiation of ‘void boost::gil::detail::RandomAccessIteratorIsMemoryBasedConcept<Iterator>::constraints() [with Iterator = const unsigned char*]’:  
../../boost/concept/detail/has_constraints.hpp:32:62:   required by substitution of ‘template<class Model> boost::concepts::detail::yes boost::concepts::detail::has_constraints_(Model*, boost::concepts::detail::wrap_constraints<Model, (& Model:: constraints)>*) [with Model = boost::gil::detail::RandomAccessIteratorIsMemoryBasedConcept<const unsigned char*>]’  
../../boost/concept/detail/has_constraints.hpp:42:5:   required from ‘const bool boost::concepts::not_satisfied<boost::gil::detail::RandomAccessIteratorIsMemoryBasedConcept<const unsigned char*> >::value’  
../../boost/concept/detail/has_constraints.hpp:45:51:   required from ‘struct boost::concepts::not_satisfied<boost::gil::detail::RandomAccessIteratorIsMemoryBasedConcept<const unsigned char*> >’  
../../boost/concept/detail/general.hpp:51:8:   required from ‘struct boost::concepts::requirement_<void (*)(boost::gil::detail::RandomAccessIteratorIsMemoryBasedConcept<const unsigned char*>)>’  
../../boost/concept_check.hpp:50:7:   required from ‘void boost::function_requires(Model*) [with Model = boost::gil::detail::RandomAccessIteratorIsMemoryBasedConcept<const unsigned char*>]’  
../../boost/gil/concepts/concept_check.hpp:45:62:   [ skipping 14 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
../../boost/gil/locator.hpp:229:5:   required from ‘class boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<const unsigned char*> >’  
../../boost/gil/image_view.hpp:55:48:   required from ‘class boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<const unsigned char*> > >’  
../../boost/gil/image_view.hpp:68:54:   required from ‘class boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<unsigned char*> > >’  
../../boost/gil/image.hpp:47:50:   required from ‘class boost::gil::image<unsigned char, false, std::allocator<unsigned char> >’  
../../boost/gil/extension/toolbox/image_types/indexed_image.hpp:245:54:   required from ‘class boost::gil::indexed_image<unsigned char, boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > > >’  
toolbox/test/indexed_image_test.cpp:30:62:   required from here  
../../boost/gil/concepts/pixel_iterator.hpp:94:41: error: ‘memunit_step’ was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]  
         std::ptrdiff_t bs = memunit_step(it);  
                                         ^  
In file included from ../../boost/gil/rgb.hpp:12:0,  
                 from ../../boost/gil/typedefs.hpp:16,  
                 from ../../boost/gil/channel_algorithm.hpp:13,  
                 from ../../boost/gil/color_convert.hpp:11,  
                 from ../../boost/gil/image_view_factory.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:15,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/planar_pixel_iterator.hpp:167:23: note: ‘template<class IC, class C> std::ptrdiff_t boost::gil::memunit_step(const boost::gil::planar_pixel_iterator<IC, C>&)’ declared here, later in the translation unit  
 inline std::ptrdiff_t memunit_step(const planar_pixel_iterator<IC,C>&) { return sizeof(typename std::iterator_traits<IC>::value_type); }  
                       ^  
In file included from ../../boost/gil/concepts/image_view.hpp:16:0,  
                 from ../../boost/gil/concepts/image.hpp:14,  
                 from ../../boost/gil/concepts.hpp:17,  
                 from ../../boost/gil/color_base_algorithm.hpp:11,  
                 from ../../boost/gil/color_base.hpp:12,  
                 from ../../boost/gil/pixel.hpp:12,  
                 from ../../boost/gil/bit_aligned_pixel_reference.hpp:11,  
                 from ../../boost/gil/bit_aligned_pixel_iterator.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:11,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/concepts/pixel_iterator.hpp:97:30: error: ‘memunit_advanced’ was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]  
         it = memunit_advanced(it, 3);  
                              ^  
In file included from ../../boost/gil/rgb.hpp:12:0,  
                 from ../../boost/gil/typedefs.hpp:16,  
                 from ../../boost/gil/channel_algorithm.hpp:13,  
                 from ../../boost/gil/color_convert.hpp:11,  
                 from ../../boost/gil/image_view_factory.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:15,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/planar_pixel_iterator.hpp:188:36: note: ‘template<class IC, class C> boost::gil::planar_pixel_iterator<IC, C> boost::gil::memunit_advanced(const boost::gil::planar_pixel_iterator<IC, C>&, std::ptrdiff_t)’ declared here, later in the translation unit  
 inline planar_pixel_iterator<IC,C> memunit_advanced(const planar_pixel_iterator<IC,C>& p, std::ptrdiff_t diff) {  
                                    ^  
In file included from ../../boost/gil/concepts/image_view.hpp:16:0,  
                 from ../../boost/gil/concepts/image.hpp:14,  
                 from ../../boost/gil/concepts.hpp:17,  
                 from ../../boost/gil/color_base_algorithm.hpp:11,  
                 from ../../boost/gil/color_base.hpp:12,  
                 from ../../boost/gil/pixel.hpp:12,  
                 from ../../boost/gil/bit_aligned_pixel_reference.hpp:11,  
                 from ../../boost/gil/bit_aligned_pixel_iterator.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:11,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/concepts/pixel_iterator.hpp:98:45: error: ‘memunit_distance’ was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]  
         std::ptrdiff_t bd = memunit_distance(it, it);  
                                             ^  
In file included from ../../boost/gil/rgb.hpp:12:0,  
                 from ../../boost/gil/typedefs.hpp:16,  
                 from ../../boost/gil/channel_algorithm.hpp:13,  
                 from ../../boost/gil/color_convert.hpp:11,  
                 from ../../boost/gil/image_view_factory.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:15,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/planar_pixel_iterator.hpp:170:23: note: ‘template<class IC, class C> std::ptrdiff_t boost::gil::memunit_distance(const boost::gil::planar_pixel_iterator<IC, C>&, const boost::gil::planar_pixel_iterator<IC, C>&)’ declared here, later in the translation unit  
 inline std::ptrdiff_t memunit_distance(const planar_pixel_iterator<IC,C>& p1, const planar_pixel_iterator<IC,C>& p2) {  
                       ^  
In file included from ../../boost/gil/concepts/image_view.hpp:16:0,  
                 from ../../boost/gil/concepts/image.hpp:14,  
                 from ../../boost/gil/concepts.hpp:17,  
                 from ../../boost/gil/color_base_algorithm.hpp:11,  
                 from ../../boost/gil/color_base.hpp:12,  
                 from ../../boost/gil/pixel.hpp:12,  
                 from ../../boost/gil/bit_aligned_pixel_reference.hpp:11,  
                 from ../../boost/gil/bit_aligned_pixel_iterator.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:11,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/concepts/pixel_iterator.hpp:101:24: error: ‘memunit_advance’ was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]  
         memunit_advance(it,3);  
                        ^  
In file included from ../../boost/gil/rgb.hpp:12:0,  
                 from ../../boost/gil/typedefs.hpp:16,  
                 from ../../boost/gil/channel_algorithm.hpp:13,  
                 from ../../boost/gil/color_convert.hpp:11,  
                 from ../../boost/gil/image_view_factory.hpp:11,  
                 from ../../boost/gil/algorithm.hpp:15,  
                 from ../../boost/gil.hpp:12,  
                 from toolbox/test/indexed_image_test.cpp:8:  
../../boost/gil/planar_pixel_iterator.hpp:183:13: note: ‘template<class IC, class C> void boost::gil::memunit_advance(boost::gil::planar_pixel_iterator<IC, C>&, std::ptrdiff_t)’ declared here, later in the translation unit  
 inline void memunit_advance(planar_pixel_iterator<IC,C>& p, std::ptrdiff_t diff) {  
             ^  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -std=c++11 -pedantic -fstrict-aliasing -Wcast-align -Wconversion -Wextra -Wfloat-equal -Wshadow -Wsign-promo -Wstrict-aliasing -Wunused-parameter -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_GIL_USE_CONCEPT_CHECK -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_TEST_DYN_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_TIMER_DYN_LINK=1 -DNOMINMAX -D_CRT_NONSTDC_NO_DEPRECATE -D_CRT_SECURE_NO_WARNINGS  -I"../.." -I"/mnt/d/boost.wsl" -c -o "../../bin.v2/libs/gil/toolbox/test/test.test/gcc-5.5.0/debug/threading-multi/visibility-hidden/indexed_image_test.o" "toolbox/test/indexed_image_test.cpp"  
  
...failed gcc.compile.c++ ../../bin.v2/libs/gil/toolbox/test/test.test/gcc-5.5.0/debug/threading-multi/visibility-hidden/indexed_image_test.o...  
```  
  
### Expected  behavior  
  
Successful compilation.  
  
### Environment  
  
All relevant information like:  
  
- Boost version (see `<boost/version.hpp>` ):   
- Compiler version: GCC 5.4 and clang 6.0  
- Build settings: `-DBOOST_GIL_USE_CONCEPT_CHECK=1`  
  
### References  
  
* #174 - general report on `memunit_*` missing or ambiguous; possibly, `indexed_image` is not the only place failing
