# #774 - Infinite loop in png_read_end [Closed]

> Username: Processor228  
> Created at: 2025-11-13 13:14:15 UTC  
> Updated at: 2025-12-05 20:18:07 UTC  
> Closed at: 2025-12-05 20:18:07 UTC  
> Url: https://github.com/boostorg/gil/issues/774  

### Actual behavior  
  
An infinitely printed log, originating from libpng:  
```  
libpng warning: tEXt: CRC error  
libpng warning: tEXt: CRC error  
...  
libpng warning: tEXt: CRC error  
```  
  
### Expected  behavior  
  
just a few logs, program does not end up in an infinite loop. The png file itself is a corrupted a little: this very tEXt section is annotated to be longer than the rest of the file. Thus we expect some error log that png is invalid.  
  
### C++ Minimal Working Example  
  
Also, the image file:  
<img width="86" height="86" alt="Image" src="https://github.com/user-attachments/assets/e7322f0d-c3af-447e-aa61-4f1de338f5b3" />  
  
  
```cpp  
#include <iostream>  
  
#include <boost/gil.hpp>  
#include <boost/gil/extension/io/png.hpp>  
  
namespace gil = boost::gil;  
  
int main(int argc, char** argv)  
{  
    std::cout << "Starting the reproducer!\n";  
  
    if (argc < 2) {  
        exit(EXIT_FAILURE);  
    }  
  
    std::string_view path = argv[1];  
  
    try {  
        gil::rgb8_image_t image;  
        gil::read_and_convert_image(std::string{path}, image, gil::png_tag{});  
    } catch(const std::exception &ex) {  
        std::cout << ex.what() << "\n";  
    }  
}  
```  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version: -  
- Build settings: -   
- Version (Git ref or `<boost/version.hpp>`): 1.86.0

---

## Comment 1

> Username: Processor228  
> Created at: 2025-11-13 13:26:56 UTC  
> Updated at: 2025-11-13 13:57:01 UTC  
> Url: https://github.com/boostorg/gil/issues/774#issuecomment-3527816064  

I think that problem takes its roots from png_reader_backend not checking the amount of bytes read [here](https://github.com/boostorg/gil/blob/d8c56358fae6302dd2f2e8cc496bcd4535c16183/include/boost/gil/extension/io/png/detail/reader_backend.hpp#L633), because there is an fread function used, for [file_stream_device](https://github.com/boostorg/gil/blob/d8c56358fae6302dd2f2e8cc496bcd4535c16183/include/boost/gil/io/device.hpp#L146-L160).  
  
I added such a check:  
```  
---  
 .../boost/gil/extension/io/png/detail/reader_backend.hpp | 9 +++++++--  
 1 file changed, 7 insertions(+), 2 deletions(-)  
  
diff --git a/include/boost/gil/extension/io/png/detail/reader_backend.hpp b/include/boost/gil/extension/io/png/detail/reader_backend.hpp  
index 3f49070f3..8cc7cea0a 100644  
--- a/libs/gil/include/boost/gil/extension/io/png/detail/reader_backend.hpp  
+++ b/libs/gil/include/boost/gil/extension/io/png/detail/reader_backend.hpp  
@@ -630,8 +630,13 @@ protected:  
                          , png_size_t length  
                          )  
     {  
-        static_cast<Device*>(png_get_io_ptr(png_ptr) )->read( data  
-                                                            , length );  
+        size_t check = static_cast<Device*>(png_get_io_ptr(png_ptr) )->read( data  
+                                                                           , length  
+                                                                           );  
+  
+        if (check != length)  
+            /* does a longjump to error handler set up inside boost::gil */  
+            png_error(png_ptr, "Read Error");  
     }  
   
     static void flush( png_structp png_ptr )  
--   
```  
  
this didn't really fix the issue completely: on other builds, an erroneus longjump is detected:  
  
```  
libpng warning: tEXt: CRC error  
libpng error: Read Error  
*** longjmp causes uninitialized stack frame ***: terminated  
Aborted (core dumped)  
```  
  
This crash is from [apply method](https://github.com/boostorg/gil/blob/d8c56358fae6302dd2f2e8cc496bcd4535c16183/include/boost/gil/extension/io/png/detail/read.hpp#L88)..

---

## Comment 2

> Username: Processor228  
> Created at: 2025-11-13 14:26:09 UTC  
> Updated at: 2025-11-13 14:40:27 UTC  
> Url: https://github.com/boostorg/gil/issues/774#issuecomment-3528075220  

there is also another problematic file:   
  
<img width="108" height="108" alt="Image" src="https://github.com/user-attachments/assets/50d2b1ed-0283-4b2e-9c10-9230f2620d0c" />  
  
when the above patch applied, the following are valgrind logs:  
  
```  
libpng error: IEND: CRC error  
==16497== Thread 2:  
==16497== Use of uninitialised value of size 8  
==16497==    at 0x274150: ~_Vector_base (stl_vector.h:366)  
==16497==    by 0x274150: ~vector (stl_vector.h:733)  
==16497==    by 0x274150: boost::gil::png_info_base::~png_info_base() (tags.hpp:290)  
==16497==    by 0x2743B7: ~image_read_info (tags.hpp:573)  
==16497==    by 0x2743B7: boost::gil::reader_backend<boost::gil::detail::file_stream_device<boost::gil::png_tag>, boost::gil::png_tag>::~reader_backend() (reader_backend.hpp:27)  
==16497==    by 0x2731CB: ~reader (read.hpp:40)  
==16497==    by 0x2731CB: read_and_convert_image<std::__cxx11::basic_string<char>, boost::gil::image<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mp11::mp_list<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > >, false, std::allocator<unsigned char> >, boost::gil::png_tag> (read_and_convert_image.hpp:239)  
==16497==    by 0xFFFFFFFFFFFFFFFF: ???  
==16497==   
==16497== Invalid read of size 8  
==16497==    at 0x274150: ~_Vector_base (stl_vector.h:366)  
==16497==    by 0x274150: ~vector (stl_vector.h:733)  
==16497==    by 0x274150: boost::gil::png_info_base::~png_info_base() (tags.hpp:290)  
==16497==    by 0x2743B7: ~image_read_info (tags.hpp:573)  
==16497==    by 0x2743B7: boost::gil::reader_backend<boost::gil::detail::file_stream_device<boost::gil::png_tag>, boost::gil::png_tag>::~reader_backend() (reader_backend.hpp:27)  
==16497==    by 0x2731CB: ~reader (read.hpp:40)  
==16497==    by 0x2731CB: read_and_convert_image<std::__cxx11::basic_string<char>, boost::gil::image<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mp11::mp_list<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > >, false, std::allocator<unsigned char> >, boost::gil::png_tag> (read_and_convert_image.hpp:239)  
==16497==    by 0xFFFFFFFFFFFFFFFF: ???  
==16497==  Address 0x280 is not stack'd, malloc'd or (recently) free'd  
==16497==   
==16497==   
==16497== Process terminating with default action of signal 11 (SIGSEGV): dumping core  
==16497==  Access not within mapped region at address 0x280  
==16497==    at 0x274150: ~_Vector_base (stl_vector.h:366)  
==16497==    by 0x274150: ~vector (stl_vector.h:733)  
==16497==    by 0x274150: boost::gil::png_info_base::~png_info_base() (tags.hpp:290)  
==16497==    by 0x2743B7: ~image_read_info (tags.hpp:573)  
==16497==    by 0x2743B7: boost::gil::reader_backend<boost::gil::detail::file_stream_device<boost::gil::png_tag>, boost::gil::png_tag>::~reader_backend() (reader_backend.hpp:27)  
==16497==    by 0x2731CB: ~reader (read.hpp:40)  
==16497==    by 0x2731CB: read_and_convert_image<std::__cxx11::basic_string<char>, boost::gil::image<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mp11::mp_list<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> > >, false, std::allocator<unsigned char> >, boost::gil::png_tag> (read_and_convert_image.hpp:239)  
==16497==    by 0xFFFFFFFFFFFFFFFF: ???  
==16497==  If you believe this happened as a result of a stack  
==16497==  overflow in your program's main thread (unlikely but  
==16497==  possible), you can try to increase the size of the  
==16497==  main thread stack using the --main-stacksize= flag.  
==16497==  The main thread stack size used in this run was 2097152.  
==16497==   
==16497== HEAP SUMMARY:  
==16497==     in use at exit: 10,180,229 bytes in 8,343 blocks  
==16497==   total heap usage: 77,279 allocs, 68,936 frees, 89,097,447 bytes allocated  
==16497==   
==16497== LEAK SUMMARY:  
==16497==    definitely lost: 160 bytes in 1 blocks  
==16497==    indirectly lost: 55 bytes in 1 blocks  
==16497==      possibly lost: 38,418 bytes in 375 blocks  
==16497==    still reachable: 10,141,596 bytes in 7,966 blocks  
==16497==                       of which reachable via heuristic:  
==16497==                         newarray           : 1,536 bytes in 16 blocks  
==16497==         suppressed: 0 bytes in 0 blocks  
==16497== Rerun with --leak-check=full to see details of leaked memory  
==16497==   
==16497== Use --track-origins=yes to see where uninitialised values come from  
==16497== For lists of detected and suppressed errors, rerun with: -s  
==16497== ERROR SUMMARY: 2 errors from 2 contexts (suppressed: 0 from 0)  
Segmentation fault (core dumped)  
```  
  
this is from this apply method setjmp, after the exception is thrown, the destructor of [this vector](https://github.com/boostorg/gil/blob/d8c56358fae6302dd2f2e8cc496bcd4535c16183/include/boost/gil/extension/io/png/tags.hpp#L566) segfaults
