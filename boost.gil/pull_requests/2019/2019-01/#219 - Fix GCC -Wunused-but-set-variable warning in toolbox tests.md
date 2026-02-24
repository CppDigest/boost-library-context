# #219 Fix GCC -Wunused-but-set-variable warning in toolbox tests [Merged]

> Username: mloskot  
> Created at: 2019-01-19 16:39:00 UTC  
> Updated at: 2019-01-20 17:46:25 UTC  
> Merged at: 2019-01-20 17:46:22 UTC  
> Closed at: 2019-01-20 17:46:22 UTC  
> Url: https://github.com/boostorg/gil/pull/219  

Fixes at least eight warnings issued by in the toolbox tests:  
  `warning: variable (...) set but not used [-Wunused-but-set-variable]`  
  
Add new or improve existing `BOOST_TEST` checks.  
  
### Environment  
  
All relevant information like:  
  
- Compiler version: GCC 5.5  
- Build settings: `<toolset>gcc:<cxxflags>"-std=c++11"`  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
------  
  
```  
toolbox/test/channel_view.cpp: In member function ‘void toolbox_tests::channel_view_test::test_method()’:  
toolbox/test/channel_view.cpp:26:12: warning: variable ‘red’ set but not used [-Wunused-but-set-variable]  
     view_t red = kth_channel_view<0>( const_view( img ));  
            ^  
toolbox/test/channel_view.cpp:29:20: warning: variable ‘red_’ set but not used [-Wunused-but-set-variable]  
     channel_view_t red_ = channel_view< red_t >( const_view( img ));  
                    ^  
```  
  
```  
toolbox/test/indexed_image_test.cpp: In member function ‘void toolbox_tests::index_image_test::test_method()’:  
toolbox/test/indexed_image_test.cpp:26:22: warning: variable ‘p’ set but not used [-Wunused-but-set-variable]  
         rgb8_pixel_t p = *view( img ).xy_at( 10, 10 );  
                      ^  
toolbox/test/indexed_image_test.cpp:49:23: warning: variable ‘color’ set but not used [-Wunused-but-set-variable]  
         rgb8_pixel_t  color = *img.get_palette_view().xy_at( index, 0 );  
                       ^  
toolbox/test/indexed_image_test.cpp:51:22: warning: variable ‘p’ set but not used [-Wunused-but-set-variable]  
         rgb8_pixel_t p = *view( img ).xy_at( 10, 1 );  
                      ^  
toolbox/test/indexed_image_test.cpp:73:22: warning: variable ‘color’ set but not used [-Wunused-but-set-variable]  
         rgb8_pixel_t color = *img.get_palette_view().xy_at( index, 0 );  
                      ^  
toolbox/test/indexed_image_test.cpp:75:22: warning: variable ‘p’ set but not used [-Wunused-but-set-variable]  
         rgb8_pixel_t p = *view( img ).xy_at( 10, 1 );  
                      ^  
toolbox/test/indexed_image_test.cpp:88:30: warning: variable ‘p’ set but not used [-Wunused-but-set-variable]  
                 rgb8_pixel_t p = *it;  
                              ^  
```

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-01-20 17:46:15 UTC  
> Url: https://github.com/boostorg/gil/pull/219#issuecomment-455886720  

All builds passed prior to the review. Then, I had to resolve one trivial conflict and force-push the update.  
  
Now, since all builds on Azure Pipelines have passed, I assume AppVeyor and Travis CI would pass too and I'm going to merge without waiting long time for the latter two.

---
