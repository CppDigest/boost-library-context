# #431 - Issues with endianess: test pixel_iterator fails [Open]

> Username: trex58  
> Created at: 2020-02-06 16:03:17 UTC  
> Updated at: 2020-02-06 16:06:12 UTC  
> Url: https://github.com/boostorg/gil/issues/431  

### Actual behavior  
  
v1 = 5  
  
This deals with what were described in 2010 at:  
   https://lists.boost.org/Archives/boost/2010/09/170446.php  
  
On AIX (Big Endian), test pixel_iterator (and probably others) fail.  
  
I have:   
  
```  
    Assertion failed: v1 == 6, file ../libs/gil/test/pixel_iterator.cpp, line 137  
```  
  
Instead of v1=6, I have v1=5. Values for vI, I=2..8, are wrong too.  
  
About p1/v1, this appears to be due to:  
```  
    ../boost/gil/channel.hpp:368 : return ret;  
```  
  ret=46701 = 0xb66d : d/16bit = 13/10 = 1101/2 : 3 last bits: 101/2 = 5 !!!  
  
The expected correct value (as seen on Fedora/x86_64) is:  
  
   ret=28086 = 0x6db6 : 6/16bit = 6/10 = 0110/2 : 3 last bits: 110/2 = 6 !!!  
  
On AIX, bytes are inverted for "ret" :  
   b6 6d instead of: 6d b6.   
  
I'm looking for the parts of the code to be made endianess-aware ??  
  
Possibilities:  
```  
  ? gil/bit_aligned_pixel_reference.hpp : bit_range& operator++() { ... } ?  
  ? gil/channel.hpp : struct static_copy_bytes { ... } ?  
  ? gil/channel.hpp : integer_t get() const { ... } ?  
```  
  
Value of p1:  
  
```  
AIX: (gdb) p p1  
$68 = {static bit_size = 3, static is_mutable = <optimized out>, _bit_range = {_current_byte = 0x110022510 "\266m\333", _bit_offset = 0}}  
x 0x110022510 :: 0xb66ddb00  
  
Linux: (gdb) p p1  
$21 = {static bit_size = 3, static is_mutable = <optimized out>, _bit_range = {_current_byte = 0x41beb0 "\266", <incomplete sequence \333>, _bit_offset = 0}}  
(gdb) x 0x41beb0 :: 0x00db6db6  
```  
  
### Expected  behavior  
  
v1 = 6  
  
### C++ Minimal Working Example  
  
test pixel_iterator  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Operating System: AIX 7.2  
- Compiler version: GCC 8.3.0  
- Build settings: 64bit  
- Version (Git ref or `<boost/version.hpp>`): 1.69.0
