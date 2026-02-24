# #218 Fix GCC -Wunused-local-typedefs warning in toolbox tests [Merged]

> Username: mloskot  
> Created at: 2019-01-19 15:16:41 UTC  
> Updated at: 2019-01-20 17:06:16 UTC  
> Merged at: 2019-01-20 17:06:13 UTC  
> Closed at: 2019-01-20 17:06:13 UTC  
> Url: https://github.com/boostorg/gil/pull/218  

Fixes at least three warnings issued by in the toolbox tests:  
  
```  
  warning: typedef (...) locally defined  
    but not used [-Wunused-local-typedefs]  
```  
  
### Environment  
  
- Compiler version: GCC 5.5  
- Build settings: `<toolset>gcc:<cxxflags>"-std=c++11"`  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
-------  
  
```  
toolbox/test/channel_type.cpp: In member function ‘void toolbox_tests::channel_type_test::test_method()’:  
toolbox/test/channel_type.cpp:27:69: warning: typedef ‘using channel_t = boost::lazy_enable_if_c<true, boost::gil::gen_chan_ref<unsigned int, boost::mpl::vector4_c<unsigned int, 4u, 4u, 4u, 4u>, boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> >, true> >::type’ locally defined but not used [-Wunused-local-typedefs]  
     using channel_t = channel_type<image_t::view_t::reference>::type;  
                                                                     ^  
```  
  
```  
toolbox/test/indexed_image_test.cpp: In member function ‘void toolbox_tests::index_image_view_test::test_method()’:  
toolbox/test/indexed_image_test.cpp:113:57: warning: typedef ‘using indices_loc_t = using locator = class boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector1<boost::gil::gray_color_t> > >*> >’ locally defined but not used [-Wunused-local-typedefs]  
     using indices_loc_t = gray8_image_t::view_t::locator;  
                                                         ^  
```  
```  
toolbox/test/indexed_image_test.cpp:114:56: warning: typedef ‘using palette_loc_t = using locator = class boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > >*> >’ locally defined but not used [-Wunused-local-typedefs]  
     using palette_loc_t = rgb8_image_t::view_t::locator;  
                                                        ^  
```

---
