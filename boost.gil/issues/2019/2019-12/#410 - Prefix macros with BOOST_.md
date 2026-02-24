# #410 - Prefix macros with BOOST_ [Closed]

> Username: mloskot  
> Created at: 2019-12-16 10:23:33 UTC  
> Updated at: 2020-01-17 13:42:38 UTC  
> Closed at: 2019-12-17 10:43:17 UTC  
> Url: https://github.com/boostorg/gil/issues/410  

### Actual behavior  
  
<!-- A clear description of what happens -->  
  
All those macros should be renamed with `BOOST_` prefix:  
  
```  
$ grep --include *.hpp -r "\\bGIL_" | grep define  
include/boost/gil/concepts/concept_check.hpp:    #define GIL_CLASS_REQUIRE(type_var, ns, concept) \  
include/boost/gil/concepts/concept_check.hpp:    #define GIL_CLASS_REQUIRE(type_var, ns, concept)  
include/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:#define GIL_AT_C_VALUE(z, N, text) mp11::mp_at_c<IntTypes, S+N>::value,  
include/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:#define GIL_DYNAMIC_AT_C_LIMIT 226 // size of the maximum vector to handle  
include/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:#define GIL_AT_C_LOOKUP(z, NUM, text)                                   \  
include/boost/gil/extension/dynamic_image/reduce.hpp:#define GIL_BINARY_REDUCE_LIMIT 226  
include/boost/gil/typedefs.hpp:#define GIL_DEFINE_BASE_TYPEDEFS_INTERNAL(B, CM, CS, LAYOUT)                             \  
include/boost/gil/typedefs.hpp:#define GIL_DEFINE_ALL_TYPEDEFS_INTERNAL(B, CM, CS, CS_FULL, LAYOUT)                        \  
include/boost/gil/typedefs.hpp:#define GIL_DEFINE_BASE_TYPEDEFS(B, CM, CS)                                                  \  
include/boost/gil/typedefs.hpp:#define GIL_DEFINE_ALL_TYPEDEFS(B, CM, CS)  
```  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Version (Git ref or `<boost/version.hpp>`): <=1.72.0

---

## Comment 1

> Username: ned14  
> Created at: 2019-12-16 14:41:38 UTC  
> Url: https://github.com/boostorg/gil/issues/410#issuecomment-566089803  

More than that needs fixing:  
  
```  
./boost_1_72_0/boost/gil/concepts/concept_check.hpp:// TODO: What is GIL_CLASS_REQUIRE for; Why not use BOOST_CLASS_REQUIRE?  
./boost_1_72_0/boost/gil/concepts/concept_check.hpp:    #define GIL_CLASS_REQUIRE(type_var, ns, concept) \  
./boost_1_72_0/boost/gil/concepts/concept_check.hpp:    #define GIL_CLASS_REQUIRE(type_var, ns, concept)  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:#define GIL_AT_C_VALUE(z, N, text) mp11::mp_at_c<IntTypes, S+N>::value,  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:#define GIL_DYNAMIC_AT_C_LIMIT 226 // size of the maximum vector to handle  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:#define GIL_AT_C_LOOKUP(z, NUM, text)                                   \  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                BOOST_PP_REPEAT(NUM, GIL_AT_C_VALUE, BOOST_PP_EMPTY)    \  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:        BOOST_PP_REPEAT(GIL_DYNAMIC_AT_C_LIMIT, GIL_AT_C_LOOKUP, BOOST_PP_EMPTY)  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                const std::size_t REM = SIZE % GIL_DYNAMIC_AT_C_LIMIT;  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                switch (index / GIL_DYNAMIC_AT_C_LIMIT) {  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                    case 0: return at_c_fn<0                   ,GIL_DYNAMIC_AT_C_LIMIT-1>::template apply<IntTypes,ValueType>(index);  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                    case 1: return at_c_fn<GIL_DYNAMIC_AT_C_LIMIT  ,REM                 >::template apply<IntTypes,ValueType>(index - GIL_DYNAMIC_AT_C_LIMIT);  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                const std::size_t REM = SIZE % GIL_DYNAMIC_AT_C_LIMIT;  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                switch (index / GIL_DYNAMIC_AT_C_LIMIT) {  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                    case 0: return at_c_fn<0                   ,GIL_DYNAMIC_AT_C_LIMIT-1>::template apply<IntTypes,ValueType>(index);  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                    case 1: return at_c_fn<GIL_DYNAMIC_AT_C_LIMIT  ,GIL_DYNAMIC_AT_C_LIMIT-1>::template apply<IntTypes,ValueType>(index - GIL_DYNAMIC_AT_C_LIMIT);  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                    case 2: return at_c_fn<GIL_DYNAMIC_AT_C_LIMIT*2,REM                 >::template apply<IntTypes,ValueType>(index - GIL_DYNAMIC_AT_C_LIMIT*2);  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                const std::size_t REM = SIZE % GIL_DYNAMIC_AT_C_LIMIT;  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                switch (index / GIL_DYNAMIC_AT_C_LIMIT) {  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                    case 0: return at_c_fn<0                   ,GIL_DYNAMIC_AT_C_LIMIT-1>::template apply<IntTypes,ValueType>(index);  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                    case 1: return at_c_fn<GIL_DYNAMIC_AT_C_LIMIT  ,GIL_DYNAMIC_AT_C_LIMIT-1>::template apply<IntTypes,ValueType>(index - GIL_DYNAMIC_AT_C_LIMIT);  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                    case 2: return at_c_fn<GIL_DYNAMIC_AT_C_LIMIT*2,GIL_DYNAMIC_AT_C_LIMIT-1>::template apply<IntTypes,ValueType>(index - GIL_DYNAMIC_AT_C_LIMIT*2);  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:                    case 3: return at_c_fn<GIL_DYNAMIC_AT_C_LIMIT*3,REM                 >::template apply<IntTypes,ValueType>(index - GIL_DYNAMIC_AT_C_LIMIT*3);  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:    return detail::at_c::at_c_impl<Size/GIL_DYNAMIC_AT_C_LIMIT>::template apply<IntTypes,ValueType>(index);  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:#undef GIL_AT_C_VALUE  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:#undef GIL_DYNAMIC_AT_C_LIMIT  
./boost_1_72_0/boost/gil/extension/dynamic_image/dynamic_at_c.hpp:#undef GIL_AT_C_LOOKUP  
./boost_1_72_0/boost/gil/extension/dynamic_image/reduce.hpp:#define GIL_BINARY_REDUCE_LIMIT 226  
./boost_1_72_0/boost/gil/extension/dynamic_image/reduce.hpp:/// the GIL_BINARY_REDUCE_LIMIT limit. If so, the operation is too complex to be binary-reduced and uses a specialization of binary_reduce_impl  
./boost_1_72_0/boost/gil/extension/dynamic_image/reduce.hpp:    using impl = detail::binary_reduce_impl<unary1_t,unary2_t,Op, (CROSS_SIZE>GIL_BINARY_REDUCE_LIMIT)>;  
./boost_1_72_0/boost/gil/extension/dynamic_image/reduce.hpp:#undef GIL_BINARY_REDUCE_LIMIT  
./boost_1_72_0/boost/gil/image_view_factory.hpp:    GIL_CLASS_REQUIRE(DstP, boost::gil, MutablePixelConcept)//why does it have to be mutable???  
./boost_1_72_0/boost/gil/image_view_factory.hpp:    GIL_CLASS_REQUIRE(View, boost::gil, ImageViewConcept)  
./boost_1_72_0/boost/gil/image_view_factory.hpp:    GIL_CLASS_REQUIRE(View, boost::gil, ImageViewConcept)  
./boost_1_72_0/boost/gil/iterator_from_2d.hpp:    GIL_CLASS_REQUIRE(Loc2, boost::gil, PixelLocatorConcept)  
./boost_1_72_0/boost/gil/locator.hpp:    GIL_CLASS_REQUIRE(StepIterator, boost::gil, StepIteratorConcept)  
./boost_1_72_0/boost/gil/pixel_iterator_adaptor.hpp:    GIL_CLASS_REQUIRE(Deref, boost::gil, PixelDereferenceAdaptorConcept)  
./boost_1_72_0/boost/gil/pixel_iterator_adaptor.hpp://    GIL_CLASS_REQUIRE(Deref, boost::gil, PixelDereferenceAdaptorConcept)  
./boost_1_72_0/boost/gil/step_iterator.hpp:    GIL_CLASS_REQUIRE(Iterator, boost::gil, MemoryBasedIteratorConcept)  
./boost_1_72_0/boost/gil/step_iterator.hpp:    GIL_CLASS_REQUIRE(Iterator, boost::gil, MemoryBasedIteratorConcept)  
./boost_1_72_0/boost/gil/step_iterator.hpp:    GIL_CLASS_REQUIRE(Deref, boost::gil, PixelDereferenceAdaptorConcept)  
./boost_1_72_0/boost/gil/typedefs.hpp:#define GIL_DEFINE_BASE_TYPEDEFS_INTERNAL(B, CM, CS, LAYOUT)                             \  
./boost_1_72_0/boost/gil/typedefs.hpp:#define GIL_DEFINE_ALL_TYPEDEFS_INTERNAL(B, CM, CS, CS_FULL, LAYOUT)                        \  
./boost_1_72_0/boost/gil/typedefs.hpp:    GIL_DEFINE_BASE_TYPEDEFS_INTERNAL(B, CM, CS, LAYOUT)                                    \  
./boost_1_72_0/boost/gil/typedefs.hpp:#define GIL_DEFINE_BASE_TYPEDEFS(B, CM, CS)                                                  \  
./boost_1_72_0/boost/gil/typedefs.hpp:    GIL_DEFINE_BASE_TYPEDEFS_INTERNAL(B, CM, CS, CS##_layout_t)  
./boost_1_72_0/boost/gil/typedefs.hpp:#define GIL_DEFINE_ALL_TYPEDEFS(B, CM, CS)                                                   \  
./boost_1_72_0/boost/gil/typedefs.hpp:    GIL_DEFINE_ALL_TYPEDEFS_INTERNAL(B, CM, CS, CS##_t, CS##_layout_t)  
./boost_1_72_0/libs/gil/CMakeLists.txt:option(GIL_BUILD_EXAMPLES "Build examples" ON)  
./boost_1_72_0/libs/gil/CMakeLists.txt:option(GIL_BUILD_HEADER_TESTS "Enable self-contained header tests" ON)  
./boost_1_72_0/libs/gil/CMakeLists.txt:option(GIL_ENABLE_EXT_DYNAMIC_IMAGE "Enable Dynamic Image extension, tests and examples" ON)  
./boost_1_72_0/libs/gil/CMakeLists.txt:option(GIL_ENABLE_EXT_IO "Enable IO extension, tests and examples (require libjpeg, libpng, libtiff)" ON)  
./boost_1_72_0/libs/gil/CMakeLists.txt:option(GIL_ENABLE_EXT_NUMERIC "Enable Numeric extension, tests and examples" ON)  
./boost_1_72_0/libs/gil/CMakeLists.txt:option(GIL_ENABLE_EXT_TOOLBOX "Enable Toolbox extension, tests and examples" ON)  
./boost_1_72_0/libs/gil/CMakeLists.txt:option(GIL_USE_CONAN "Use Conan to install dependencies" OFF)  
./boost_1_72_0/libs/gil/CMakeLists.txt:option(GIL_USE_CLANG_TIDY "Set CMAKE_CXX_CLANG_TIDY property on targets to enable clang-tidy linting" OFF)  
./boost_1_72_0/libs/gil/CMakeLists.txt:if(GIL_USE_CONAN)  
./boost_1_72_0/libs/gil/CMakeLists.txt:if(GIL_ENABLE_EXT_IO)  
./boost_1_72_0/libs/gil/CMakeLists.txt:  if (GIL_USE_CONAN)  
./boost_1_72_0/libs/gil/CMakeLists.txt:    set(GIL_ENABLE_EXT_IO_RAW ${LibRaw_FOUND} CACHE BOOL "Enable IO RAW extension (requires libraw)" FORCE)  
./boost_1_72_0/libs/gil/CMakeLists.txt:    if(GIL_ENABLE_EXT_IO_RAW)  
./boost_1_72_0/libs/gil/CMakeLists.txt:if(GIL_USE_CLANG_TIDY AND CMAKE_VERSION VERSION_GREATER_EQUAL 3.6)  
./boost_1_72_0/libs/gil/CMakeLists.txt:  set(GIL_BUILD_HEADER_TESTS OFF)  
./boost_1_72_0/libs/gil/CMakeLists.txt:if(GIL_BUILD_EXAMPLES AND GIL_ENABLE_EXT_IO)  
./boost_1_72_0/libs/gil/example/cmake/cmake-variants.yaml:        GIL_ENABLE_EXT_IO: no  
./boost_1_72_0/libs/gil/example/cmake/cmake-variants.yaml:        GIL_ENABLE_EXT_IO: yes  
./boost_1_72_0/libs/gil/example/cmake/cmake-variants.yaml:        GIL_ENABLE_EXT_NUMERIC: no  
./boost_1_72_0/libs/gil/example/cmake/cmake-variants.yaml:        GIL_ENABLE_EXT_NUMERIC: yes  
./boost_1_72_0/libs/gil/example/cmake/cmake-variants.yaml:        GIL_ENABLE_EXT_TOOLBOX: no  
./boost_1_72_0/libs/gil/example/cmake/cmake-variants.yaml:        GIL_ENABLE_EXT_TOOLBOX: yes  
./boost_1_72_0/libs/gil/example/cmake/cmake-variants.yaml:        GIL_BUILD_HEADER_TESTS: no  
./boost_1_72_0/libs/gil/example/cmake/cmake-variants.yaml:        GIL_BUILD_HEADER_TESTS: yes  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_EXAMPLES", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_HEADER_TESTS", "value": "OFF" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_DYNAMIC_IMAGE", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_IO", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_NUMERIC", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_TOOLBOX", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CONAN", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CLANG_TIDY", "value": "OFF" }  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_EXAMPLES", "value": "OFF" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_HEADER_TESTS", "value": "OFF" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_DYNAMIC_IMAGE", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_IO", "value": "OFF" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_NUMERIC", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_TOOLBOX", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CONAN", "value": "OFF" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CLANG_TIDY", "value": "OFF" }  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_EXAMPLES", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_HEADER_TESTS", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_DYNAMIC_IMAGE", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_IO", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_NUMERIC", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_TOOLBOX", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CONAN", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CLANG_TIDY", "value": "OFF" }  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_EXAMPLES", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_HEADER_TESTS", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_DYNAMIC_IMAGE", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_IO", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_NUMERIC", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_TOOLBOX", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CONAN", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CLANG_TIDY", "value": "OFF" }  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_EXAMPLES", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_HEADER_TESTS", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_DYNAMIC_IMAGE", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_IO", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_NUMERIC", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_TOOLBOX", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CONAN", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CLANG_TIDY", "value": "OFF" }  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_EXAMPLES", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_HEADER_TESTS", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_DYNAMIC_IMAGE", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_IO", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_NUMERIC", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_TOOLBOX", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CONAN", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CLANG_TIDY", "value": "OFF" }  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_EXAMPLES", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_HEADER_TESTS", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_DYNAMIC_IMAGE", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_IO", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_NUMERIC", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_TOOLBOX", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CONAN", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CLANG_TIDY", "value": "OFF" }  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_EXAMPLES", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_HEADER_TESTS", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_DYNAMIC_IMAGE", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_IO", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_NUMERIC", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_TOOLBOX", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CONAN", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CLANG_TIDY", "value": "OFF" }  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_EXAMPLES", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_HEADER_TESTS", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_DYNAMIC_IMAGE", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_IO", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_NUMERIC", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_TOOLBOX", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CONAN", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CLANG_TIDY", "value": "OFF" }  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_EXAMPLES", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_HEADER_TESTS", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_DYNAMIC_IMAGE", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_IO", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_NUMERIC", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_TOOLBOX", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CONAN", "value": "OFF" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CLANG_TIDY", "value": "OFF" }  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_EXAMPLES", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_HEADER_TESTS", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_DYNAMIC_IMAGE", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_IO", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_NUMERIC", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_TOOLBOX", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CONAN", "value": "OFF" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CLANG_TIDY", "value": "OFF" }  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_EXAMPLES", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_HEADER_TESTS", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_DYNAMIC_IMAGE", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_IO", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_NUMERIC", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_TOOLBOX", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CONAN", "value": "OFF" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CLANG_TIDY", "value": "OFF" }  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_EXAMPLES", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_BUILD_HEADER_TESTS", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_DYNAMIC_IMAGE", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_IO", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_NUMERIC", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_ENABLE_EXT_TOOLBOX", "value": "ON" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CONAN", "value": "OFF" },  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json:        { "name": "GIL_USE_CLANG_TIDY", "value": "OFF" }  
./boost_1_72_0/libs/gil/example/CMakeLists.txt:  if(GIL_USE_CONAN)  
./boost_1_72_0/libs/gil/test/CMakeLists.txt:# if(GIL_BUILD_HEADERS_TESTS)  
./boost_1_72_0/libs/gil/test/core/channel/algorithm_channel_convert.cpp:#define GIL_TEST_CHANNEL_CONVERT_FROM(source_channel_type) \  
./boost_1_72_0/libs/gil/test/core/channel/algorithm_channel_convert.cpp:#undef GIL_TEST_CHANNEL_CONVERT_FROM  
./boost_1_72_0/libs/gil/test/extension/CMakeLists.txt:if(GIL_ENABLE_EXT_DYNAMIC_IMAGE)  
./boost_1_72_0/libs/gil/test/extension/CMakeLists.txt:if(GIL_ENABLE_EXT_NUMERIC)  
./boost_1_72_0/libs/gil/test/extension/CMakeLists.txt:if(GIL_ENABLE_EXT_TOOLBOX)  
./boost_1_72_0/libs/gil/test/extension/CMakeLists.txt:if(GIL_ENABLE_EXT_IO)  
./boost_1_72_0/libs/gil/test/extension/io/CMakeLists.txt:if(GIL_ENABLE_EXT_IO_RAW)  
./boost_1_72_0/libs/gil/test/extension/io/CMakeLists.txt:if(GIL_ENABLE_EXT_IO_RAW)  
./boost_1_72_0/libs/gil/test/header/CMakeLists.txt:if(GIL_ENABLE_EXT_NUMERIC)  
./boost_1_72_0/libs/gil/test/header/CMakeLists.txt:if(GIL_ENABLE_EXT_TOOLBOX)  
./boost_1_72_0/libs/gil/test/header/CMakeLists.txt:if(GIL_ENABLE_EXT_IO)  
./boost_1_72_0/libs/gil/test/header/CMakeLists.txt:if(NOT GIL_ENABLE_EXT_IO_RAW)  
./boost_1_72_0/libs/gil/test/legacy/performance.cpp:BOOST_AUTO_TEST_SUITE(GIL_Tests)  
./boost_1_72_0/libs/gil/test/legacy/recreate_image.cpp:BOOST_AUTO_TEST_SUITE(GIL_Tests)  
```  
  
The reason cmake also must be `BOOST_` prefixed is because cmake gets consumed by external third party cmake.

---

## Comment 2

> Username: mloskot  
> Created at: 2019-12-16 16:22:04 UTC  
> Url: https://github.com/boostorg/gil/issues/410#issuecomment-566133063  

@ned14   
> More than that needs fixing:  
  
The description dumped only  places of `define` of macros that need to be replaced.  
Obviously, those need to be fixed in places of usage too.  
  
Why are you requesting changes in CMake, YAML, Visual Studio auxiliary files? i.e.  
  
```  
./boost_1_72_0/libs/gil/CMakeLists.txt  
./boost_1_72_0/libs/gil/example/cmake/cmake-variants.yaml  
./boost_1_72_0/libs/gil/example/cmake/CMakeSettings.json  
```

---

## Comment 3

> Username: ned14  
> Created at: 2019-12-16 16:39:07 UTC  
> Url: https://github.com/boostorg/gil/issues/410#issuecomment-566140272  

I was not sure if you expect your cmake to get included by external third party cmake. If so, you need to prefix, if it will never happen, you can ignore the request.

---

## Comment 4

> Username: mloskot  
> Created at: 2019-12-16 16:43:19 UTC  
> Url: https://github.com/boostorg/gil/issues/410#issuecomment-566142056  

Currently, we do not guarantee such usage as per the [CONTRIBUTING.md](https://github.com/boostorg/gil/blob/9f5f24affd11217207126e2da94355c992fa861e/CONTRIBUTING.md#using-cmake):  
  
> WARNING: The CMake configuration is only provided for convenience of contributors.  
  
However, good idea, I will review the full list.

---

## Comment 5

> Username: mloskot  
> Created at: 2020-01-16 19:15:47 UTC  
> Url: https://github.com/boostorg/gil/issues/410#issuecomment-575301741  

@ned14 https://github.com/boostorg/gil/issues/410#issuecomment-566140272 on prefixing CMake parameters with `BOOST_` too has been addressed by @sourabhdharpure in PR #419

---

## Comment 6

> Username: ned14  
> Created at: 2020-01-17 13:42:38 UTC  
> Url: https://github.com/boostorg/gil/issues/410#issuecomment-575630700  

Thanks for all your work on this.
