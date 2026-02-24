# #173 - Managed shared memory segment value not aligned [Closed]

> Username: golderweb  
> Created at: 2022-04-12 09:48:37 UTC  
> Updated at: 2024-10-03 08:11:08 UTC  
> Closed at: 2024-10-03 08:11:07 UTC  
> Url: https://github.com/boostorg/interprocess/issues/173  

On our 32bit armv7l system the value object of shared memory segments created with managed_shared_memory::construct is not properly aligned.  
  
The pointer to the value object is calculated in [boost/interprocess/detail/segment_manager_helper.hhp](https://github.com/boostorg/interprocess/blob/develop/include/boost/interprocess/detail/segment_manager_helper.hpp#L146) (line 146 and following). This is done based on `block_header::value_offset()`.  
  
````c++  
   void *value() const  
   {  
      return const_cast<char*>((reinterpret_cast<const char*>(this) + this->value_offset()));  
   }  
  
   size_type value_offset() const  
   {  
      return get_rounded_size(size_type(sizeof(block_header<size_type>)), size_type(m_value_alignment));  
   }  
````  
  
This `value_offset()` tries to ensure proper alignment. But since the location of the block_header object is not guaranteed to be aligned for value type, the value object may end beeing unaligned.  
  
The following minimal example triggers the bug.  
  
````c++  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <string>  
#include <iostream>  
  
typedef struct head {  
   uint32_t a;  
   uint32_t b;  
   struct{  
      uint64_t tv_sec;  
      uint32_t tv_nsec;  
   } time;  
} head_t;  
  
typedef struct container64{  
   head_t head;  
   uint32_t a;  
} container64_t;  
  
  
int main(){  
  
   std::string shmName("TestSHM");  
  
   boost::interprocess::shared_memory_object::remove(shmName.c_str());  
   boost::interprocess::managed_shared_memory managedShm(boost::interprocess::create_only, shmName.c_str(), 4096);  
  
   // Create container  
   auto container64 = managedShm.construct<container64_t>("Container64")();  
   container64->a = 42;  
  
   // Access container  
   std::cerr << "Container64 data.a: " << container64->a << std::endl;  
}  
````  
  
Compiler and flags:  
arm-linux-gnueabi-g++ (GCC) 7.3.0  
arm-linux-gnueabi-g++  -march=armv7-a -marm -mfpu=neon -mfloat-abi=hard -mcpu=cortex-a8  -O0 -g3 -Wall -c -pthread  
  
`CtorArgN::construct()` from [boost/interprocess/detail/named_proxy.hpp](https://github.com/boostorg/interprocess/blob/develop/include/boost/interprocess/detail/named_proxy.hpp#L81) results in following asembler code. The instruction `0004ca50: vst1.8 {d16-d17], [r3 :64]` fails, since address in register r3 is not 64 bit aligned. This results in an alignment trap and programm beeing terminated by SIGBUS=0x7 signal.  
  
````asm  
;{  ::new((void*)mem, boost_container_new_t())T(boost::forward<Args>(get<IdxPack>(args_))...); }  
0004ca38:   ldrb    r2, [r11, #-12]  
0004ca3c:   ldr     r1, [r11, #-20] ; 0xffffffec  
0004ca40:   mov     r0, #32  
0004ca44:   bl      0x12664 <operator new(unsigned int, void*, boost_container_new_t)>  
0004ca48:   mov     r3, r0  
0004ca4c:   vmov.i32        q8, #0  ; 0x00000000  
0004ca50:   vst1.8  {d16-d17}, [r3 :64]  
0004ca54:   vorr    d18, d16, d16  
0004ca58:   vstr    d18, [r3, #16]  
0004ca5c:   vstr    d16, [r3, #24]  
0004ca60:   nop     {0}  
0004ca64:   sub     sp, r11, #4  
0004ca68:   pop     {r11, pc}  
´´´´  
  
Tested with interprocess library from Boost 1.72.0 and 1.78.0 (current release)

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-10-03 08:11:07 UTC  
> Url: https://github.com/boostorg/interprocess/issues/173#issuecomment-2390790727  

Many thanks for the report and the test case! This issue is similar to #217 but triggered without overalignment.  
  
For Boost 1.87 Interprocess will implement a new ABI that fixes this issue. The commit implementing the new ABI is https://github.com/boostorg/interprocess/commit/cbeb5b5ea9d363484306b33c0a0ad304914ed3f3 with additional space optimizations in https://github.com/boostorg/interprocess/commit/94bc4e11860d3be216d1c1c248c4cef000d2fc96  
  
Unfortunately it was not possible to fix the bug without breaking ABI, you can #define BOOST_INTERPROCESS_SEGMENT_MANAGER_ABI to 1 to use the old, broken ABI, that will remain in the library for some releases.
