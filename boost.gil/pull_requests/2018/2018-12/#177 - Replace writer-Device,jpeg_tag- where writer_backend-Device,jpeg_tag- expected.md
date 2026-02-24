# #177 Replace writer<Device,jpeg_tag> where writer_backend<Device,jpeg_tag> expected [Merged]

> Username: mloskot  
> Created at: 2018-12-09 13:17:27 UTC  
> Updated at: 2018-12-09 14:46:03 UTC  
> Merged at: 2018-12-09 14:45:52 UTC  
> Closed at: 2018-12-09 14:45:52 UTC  
> Url: https://github.com/boostorg/gil/pull/177  

The reference to `writer<Device,jpeg_tag>` is clearly incorrect:  
- writer has no members `init_device`, `empty_buffer`, `closed_buffer`  
- if use of writer was correct there, it would require  
  `#include <boost/gil/extension/io/jpeg/detail/writer.hpp>`, but that  
  would impose cyclic-dependency between writer.hpp and writer_backend.hpp.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
-----  
  
How on earth it's never been noticed?! Is the fix actually correct or am I completely confused myself?  
  
```  
cmake --build . --target test_compile_headers  
...  
[ 15%] Building CXX object test/headers/CMakeFiles/test_header_extension-io-jpeg-write.dir/extension-io-jpeg-write.cpp.o  
In file included from /mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/write.hpp:13:0,  
                 from /mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/write.hpp:13,  
                 from /mnt/d/boost.wsl/libs/gil/_build.gcc5/test/headers/extension-io-jpeg-write.cpp:14:  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp: In constructor ‘boost::gil::writer_backend<Device, boost::gil::jpeg_tag>::writer_backend(const Device&, const boost::gil::image_write_info<boost::gil::jpeg_tag>&)’:  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:98:29: error: ‘writer’ was not declared in this scope  
         _jerr.error_exit = &writer< Device, jpeg_tag >::error_exit;  
                             ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:98:43: error: expected primary-expression before ‘,’ token  
         _jerr.error_exit = &writer< Device, jpeg_tag >::error_exit;  
                                           ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:98:54: error: expected primary-expression before ‘>’ token  
         _jerr.error_exit = &writer< Device, jpeg_tag >::error_exit;  
                                                      ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:98:55: error: ‘::error_exit’ has not been declared  
         _jerr.error_exit = &writer< Device, jpeg_tag >::error_exit;  
                                                       ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:105:109: error: expected primary-expression before ‘,’ token  
         _dest._jdest.init_destination    = reinterpret_cast< void(*)   ( j_compress_ptr ) >( &writer< Device, jpeg_tag >::init_device  );  
                                                                                                             ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:105:120: error: expected primary-expression before ‘>’ token  
         _dest._jdest.init_destination    = reinterpret_cast< void(*)   ( j_compress_ptr ) >( &writer< Device, jpeg_tag >::init_device  );  
                                                                                                                        ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:105:121: error: ‘::init_device’ has not been declared  
         _dest._jdest.init_destination    = reinterpret_cast< void(*)   ( j_compress_ptr ) >( &writer< Device, jpeg_tag >::init_device  );  
                                                                                                                         ^  
In file included from /mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/write.hpp:13:0,  
                 from /mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/write.hpp:13,  
                 from /mnt/d/boost.wsl/libs/gil/_build.gcc5/test/headers/extension-io-jpeg-write.cpp:14:  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:106:109: error: expected primary-expression before ‘,’ token  
         _dest._jdest.empty_output_buffer = reinterpret_cast< boolean(*)( j_compress_ptr ) >( &writer< Device, jpeg_tag >::empty_buffer );  
                                                                                                             ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:106:120: error: expected primary-expression before ‘>’ token  
         _dest._jdest.empty_output_buffer = reinterpret_cast< boolean(*)( j_compress_ptr ) >( &writer< Device, jpeg_tag >::empty_buffer );  
                                                                                                                        ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:106:121: error: ‘::empty_buffer’ has not been declared  
         _dest._jdest.empty_output_buffer = reinterpret_cast< boolean(*)( j_compress_ptr ) >( &writer< Device, jpeg_tag >::empty_buffer );  
                                                                                                                         ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:107:109: error: expected primary-expression before ‘,’ token  
         _dest._jdest.term_destination    = reinterpret_cast< void(*)   ( j_compress_ptr ) >( &writer< Device, jpeg_tag >::close_device );  
                                                                                                             ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:107:120: error: expected primary-expression before ‘>’ token  
         _dest._jdest.term_destination    = reinterpret_cast< void(*)   ( j_compress_ptr ) >( &writer< Device, jpeg_tag >::close_device );  
                                                                                                                        ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:107:121: error: ‘::close_device’ has not been declared  
         _dest._jdest.term_destination    = reinterpret_cast< void(*)   ( j_compress_ptr ) >( &writer< Device, jpeg_tag >::close_device );  
                                                                                                                         ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp: In static member function ‘static boolean boost::gil::writer_backend<Device, boost::gil::jpeg_tag>::empty_buffer(jpeg_compress_struct*)’:  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:146:9: error: ‘writer’ was not declared in this scope  
         writer<Device,jpeg_tag>::init_device( cinfo );  
         ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:146:22: error: expected primary-expression before ‘,’ token  
         writer<Device,jpeg_tag>::init_device( cinfo );  
                      ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:146:31: error: expected primary-expression before ‘>’ token  
         writer<Device,jpeg_tag>::init_device( cinfo );  
                               ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:146:32: error: ‘::init_device’ has not been declared  
         writer<Device,jpeg_tag>::init_device( cinfo );  
                                ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp: In static member function ‘static void boost::gil::writer_backend<Device, boost::gil::jpeg_tag>::error_exit(j_common_ptr)’:  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:168:9: error: ‘writer’ was not declared in this scope  
         writer< Device, jpeg_tag >* mgr = reinterpret_cast< writer< Device, jpeg_tag >* >( cinfo->client_data );  
         ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:168:23: error: expected primary-expression before ‘,’ token  
         writer< Device, jpeg_tag >* mgr = reinterpret_cast< writer< Device, jpeg_tag >* >( cinfo->client_data );  
                       ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:168:34: error: expected primary-expression before ‘>’ token  
         writer< Device, jpeg_tag >* mgr = reinterpret_cast< writer< Device, jpeg_tag >* >( cinfo->client_data );  
                                  ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:168:37: error: ‘mgr’ was not declared in this scope  
         writer< Device, jpeg_tag >* mgr = reinterpret_cast< writer< Device, jpeg_tag >* >( cinfo->client_data );  
                                     ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:168:61: error: ‘writer’ does not name a type  
         writer< Device, jpeg_tag >* mgr = reinterpret_cast< writer< Device, jpeg_tag >* >( cinfo->client_data );  
                                                             ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:168:67: error: expected ‘>’ before ‘<’ token  
         writer< Device, jpeg_tag >* mgr = reinterpret_cast< writer< Device, jpeg_tag >* >( cinfo->client_data );  
                                                                   ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:168:67: error: expected ‘(’ before ‘<’ token  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:168:67: error: expected primary-expression before ‘<’ token  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:168:75: error: expected primary-expression before ‘,’ token  
         writer< Device, jpeg_tag >* mgr = reinterpret_cast< writer< Device, jpeg_tag >* >( cinfo->client_data );  
                                                                           ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:168:86: error: expected primary-expression before ‘>’ token  
         writer< Device, jpeg_tag >* mgr = reinterpret_cast< writer< Device, jpeg_tag >* >( cinfo->client_data );  
                                                                                      ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:168:89: error: expected primary-expression before ‘>’ token  
         writer< Device, jpeg_tag >* mgr = reinterpret_cast< writer< Device, jpeg_tag >* >( cinfo->client_data );  
                                                                                         ^  
/mnt/d/boost.wsl/libs/gil/include/boost/gil/extension/io/jpeg/detail/writer_backend.hpp:168:112: error: expected ‘)’ before ‘;’ token  
         writer< Device, jpeg_tag >* mgr = reinterpret_cast< writer< Device, jpeg_tag >* >( cinfo->client_data );  
                                                                                                                ^  
```

---
