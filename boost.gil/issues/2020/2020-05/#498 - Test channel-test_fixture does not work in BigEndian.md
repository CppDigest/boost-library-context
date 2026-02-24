# #498 - Test channel/test_fixture does not work in BigEndian [Open]

> Username: trex58  
> Created at: 2020-05-19 16:31:17 UTC  
> Updated at: 2020-05-29 09:48:43 UTC  
> Url: https://github.com/boostorg/gil/issues/498  

[Issue200.txt](https://github.com/boostorg/gil/files/4651702/Issue200.txt)  
  
### Actual behavior  
  
Out of the 3 test_fixture tests, the "channel" one fails on AIX, which is BigEndian.  
These 3 tests work perfectly both on Fedora/x86_64 and Fedora/PPC64LE , which are both LittleEndian.  
This issue on AIX occurs with "unsigned int" and "long unsigned int" only. Not with "unsigned short" nor "unsigned long". 4 individual tests are failing.  
  
I think that the issue comes from LittleEndian vs BigEndian. However, I haven't yet found the exact place and the root issue. Thus, I provide in the attached file traces from gdb done on AIX and Fedora/x86_64 . I hope that it may provide enough data for Boost.gil expert to understand the exact place where comes the issue.  
  
./libs/gil/test/core/channel/test_fixture.cpp :  
+204      std::uint16_t data_ = 0;  
On Fedora/x86_64, a 16bit integer is used on the right part of a 32bit word. Though on AIX it makes use of the left part of the 32bit word. Moreover, addresses are not addressed in the same way (left to right on AIX). That may lead to issues.  
  
On AIX, for "unsigned short", I see that copies are done that change data_ to 0xffff... , which is 65535 and is correct. However, for "unsigned int", I see that several attempts are done to write in data_ :  however, each time the value to be copied is 0 , thus data_ never is 65535 but 0x0000.... instead .  
On Fedora/x86_64, that works fine.  
  
### Expected  behavior  
  
<!-- A clear and concise description of what you expected to happen. -->  
  
fix.data_ is expected to be 65535 though it is 0 for "unsigned int" cases.  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
The test_fixture shows the issue perfectly. No shorter program available for now.  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version: GCC 8.4 or 9.3  
- Build settings: g++ -O0 -g -gdwarf -fvisibility-inlines-hidden -fPIC -maix64 -pthread -mcmodel=large -fno-inline -Wall -fvisibility=hidden -ffp-contract=off -ftls-model=initial-exec -fstrict-aliasing -pedantic -Wextra -Wcast-align -Wconversion -Wfloat-equal -Wshadow -Wsign-promo -Wstrict-aliasing -Wunused-parameter -DBOOST_ALL_NO_LIB=1 -DBOOST_BEAST_ALLOW_DEPRECATED -DBOOST_BEAST_SEPARATE_COMPILATION -DBOOST_GIL_USE_CONCEPT_CHECK=1 -I.. -I../libs/gil/test  
- Version (Git ref or `<boost/version.hpp>`): 1.73.0

---

## Comment 1

> Username: trex58  
> Created at: 2020-05-29 08:27:23 UTC  
> Url: https://github.com/boostorg/gil/issues/498#issuecomment-635844564  

First issue in the code.  
  
On a Fedora31/x86_64 machine, I have this:  
  
**../boost/gil/channel.hpp:**  
```  
template <std::size_t K>  
struct static_copy_bytes  
{  
    void operator()(unsigned char const* from, unsigned char* to) const  
    {  
printf("     static_copy_bytes:  from: %p  %3u 0x%2hhx   to: %p  %3u 0x%2hhx Bef\n", from, *from, *from, to, *to , *to);  
        *to = *from;  
printf("     static_copy_bytes:  from: %p  %3u 0x%2hhx   to: %p  %3u 0x%2hhx Aft\n", from, *from, *from, to, *to , *to);  
        static_copy_bytes<K - 1>()(++from, ++to);  
    }  
};  
.......  
    bitfield_t get_data() const {  
        bitfield_t ret;  
        printf(" get_data     sizeof(bitfield_t): %d  sizeof(data_ptr_t): %d   sizeof(*data_ptr_t): %d \n", sizeof(bitfield_t), sizeof(data_ptr_t), sizeof(d  
ata_ptr_t *) );  
        printf("        data_ptr_t  _data_ptr :  %p *_data_ptr: %d 0x%x \n",  _data_ptr , * static_cast<std::uint16_t *>(_data_ptr), * static_cast<std::uint  
16_t *>(_data_ptr) );  
        printf("                    &ret      :  %p \n",                                      &ret );  
        static_copy_bytes<sizeof(bitfield_t) >()(gil_reinterpret_cast_c<const unsigned char*>(_data_ptr),gil_reinterpret_cast<unsigned char*>(&ret));  
        printf("        bitfield_t  ret       :   %10d 0x%x \n\n", ret, ret);  
        return ret;  
    }  
  
    void set_data(const bitfield_t& val) const {  
        printf(" set_data  &val:      %p   val:        %d\n", &val, val);  
        static_copy_bytes<sizeof(bitfield_t) >()(gil_reinterpret_cast_c<const unsigned char*>(&val),gil_reinterpret_cast<unsigned char*>(_data_ptr));  
        printf(" set_data  _data_ptr: %p   *_data_ptr: %d\n", _data_ptr, *((int *)_data_ptr) );  
    }  
  
```  
  
**libs/gil/test/core/channel/test_fixture.hpp** :  
```  
template <typename BitField>  
struct packed_channels565  
{  
...  
    std::uint16_t data_ = 0;  
...  
    packed_channels565() : channel1_(&data_), channel2_(&data_), channel3_(&data_)  
    {  
printf(" packed_channels565:  sizeof(struct packed_channels565) %d   &data_: %p \n", sizeof(struct packed_channels565), &data_ );  
printf(" packed_channels565:                                          data_: %u 0x%x  All: %d \n", data_, data_, *((unsigned int *) &data_ ) );  
for(int i=0 ; i<4 ; i++)  
{  
        printf("     data_[%d]: 0x%2hhx \n", i, *(((unsigned char *)(&data_))+i) );  
}  
```  
giving the result:  
```  
packed_channels565:  sizeof(struct packed_channels565) 32   &data_: 0x7ffebcec1a60  
 packed_channels565:                                          data_: 0 0x0  All: 0  
     data_[0]: 0x 0  
     data_[1]: 0x 0  
     data_[2]: 0x 0  
     data_[3]: 0x 0  
  
....................  
  
 get_data     sizeof(bitfield_t): 4  sizeof(data_ptr_t): 8   sizeof(*data_ptr_t): 8  
        data_ptr_t  _data_ptr :  0x7ffebcec1a60 *_data_ptr: 0 0x0  
                    &ret      :  0x7ffebcec1988  
     static_copy_bytes:  from: 0x7ffebcec1a60    0 0x 0   to: 0x7ffebcec1988  142 0x8e Bef  
     static_copy_bytes:  from: 0x7ffebcec1a60    0 0x 0   to: 0x7ffebcec1988    0 0x 0 Aft  
     static_copy_bytes:  from: 0x7ffebcec1a61    0 0x 0   to: 0x7ffebcec1989   18 0x12 Bef  
     static_copy_bytes:  from: 0x7ffebcec1a61    0 0x 0   to: 0x7ffebcec1989    0 0x 0 Aft  
     static_copy_bytes:  from: 0x7ffebcec1a62    0 0x 0   to: 0x7ffebcec198a   64 0x40 Bef  
     static_copy_bytes:  from: 0x7ffebcec1a62    0 0x 0   to: 0x7ffebcec198a    0 0x 0 Aft  
     static_copy_bytes:  from: 0x7ffebcec1a63    0 0x 0   to: 0x7ffebcec198b    0 0x 0 Bef  
     static_copy_bytes:  from: 0x7ffebcec1a63    0 0x 0   to: 0x7ffebcec198b    0 0x 0 Aft  
        bitfield_t  ret       :            0 0x0  
```  
  
I mean to say:  
- data_ is defined as a half 32bit integer: a short  
- address of data_ is: 0x7ffebcec1a60  but only bytes 0x7ffebcec1a60 and 0x7ffebcec1a61 are initialized to 0x0 thanks to the line:   std::uint16_t data_ = 0;  
- during the test, values are copied from 0x7ffebcec1a60+ places to another memory place, and vice-versa.  
- at the beginning, for "unsigned short", only 2 bytes are copied to and from the 2 memory places, using the 2 bytes of 0x7ffebcec1a60 and 0x7ffebcec1a61 .  
- however, at a moment (for "unsigned int"), values from the 4 bytes starting at 0x7ffebcec1a60 are copied to the second memory place, thus copying from 0x7ffebcec1a60 to 0x7ffebcec1a63 .  
  **BUT** only the 2 first bytes have been initialized by the line:   std::uint16_t data_ = 0;    !!!  
  Though, on Fedora, the whole 32bit word contains 0 at the beginning, it is not the case on AIX: the 4th bytes contains: 0x1 :    
```  
 packed_channels565:  sizeof(struct packed_channels565) 32   &data_: ffffffffffff498  
 packed_channels565:                                          data_: 0 0x0  All: 1  
     data_[0]: 0x 0  
     data_[1]: 0x 0  
     data_[2]: 0x 0  
     data_[3]: 0x 1  
```  
Thus, a non-null value appears when running the test on AIX though on Fedora/Intel, a 0 is copied. The test works on Intel thanks to the memory being cleaned, full of 0. It is not the case for AIX. And the program should not expect the memory to be clean.  
  
I think that there is an first issue in the algorithm, where the whole word where **data_** is created should be initialized by 0 and not only the 2 bytes of data_ .

---

## Comment 2

> Username: trex58  
> Created at: 2020-05-29 08:50:08 UTC  
> Updated at: 2020-05-29 09:48:43 UTC  
> Url: https://github.com/boostorg/gil/issues/498#issuecomment-635855433  

Second issue in the code.  
  
**../libs/gil/test/core/channel/test_fixture.cpp:**  
```  
struct test_packed_channels565  
{  
    template <typename BitField>  
    void operator()(BitField const &)  
    {  
        using bitfield_t = BitField;  
        static_assert(std::is_integral<bitfield_t>::value, "bitfield is not integral type");  
  
        // Regardless of BitField buffer bit-size, the fixture is initialized  
        // with max value that fits into 5+6+5 bit integer  
        fixture::packed_channels565<bitfield_t> fix;  
        fixture::channel_minmax_value<std::uint16_t> exp;  
printf("test_packed_channels565:         &fix.data_: %p  &exp.max_v_: %p \n", &fix.data_, &exp.max_v_ );  
printf("test_packed_channels565:          fix.data_: %d   exp.max_v_: %d \n",  fix.data_,  exp.max_v_ );  
        BOOST_TEST_EQ(fix.data_, exp.max_v_);  
    }  
...  
```  
On a Fedora31/x86_64 machine, I have this for "unsigned int" :  
```  
packed_channels565:  sizeof(struct packed_channels565) 32   &data_: 0x7ffebcec1a60  
  
......  
  
 set_data  &val:      0x7ffebcec19c8   val:        65535  
     static_copy_bytes:  from: 0x7ffebcec19c8  255 0xff   to: 0x7ffebcec1a60  255 0xff Bef  
     static_copy_bytes:  from: 0x7ffebcec19c8  255 0xff   to: 0x7ffebcec1a60  255 0xff Aft  
     static_copy_bytes:  from: 0x7ffebcec19c9  255 0xff   to: 0x7ffebcec1a61    7 0x 7 Bef  
     static_copy_bytes:  from: 0x7ffebcec19c9  255 0xff   to: 0x7ffebcec1a61  255 0xff Aft  
     static_copy_bytes:  from: 0x7ffebcec19ca    0 0x 0   to: 0x7ffebcec1a62    0 0x 0 Bef  
     static_copy_bytes:  from: 0x7ffebcec19ca    0 0x 0   to: 0x7ffebcec1a62    0 0x 0 Aft  
     static_copy_bytes:  from: 0x7ffebcec19cb    0 0x 0   to: 0x7ffebcec1a63    0 0x 0 Bef  
     static_copy_bytes:  from: 0x7ffebcec19cb    0 0x 0   to: 0x7ffebcec1a63    0 0x 0 Aft  
 set_data  _data_ptr: 0x7ffebcec1a60   *_data_ptr: 65535  
test_packed_channels565:         &fix.data_: 0x7ffebcec1a60  &exp.max_v_: 0x7ffebcec1a5e  
test_packed_channels565:          fix.data_: 65535   exp.max_v_: 65535  
  
```  
And thus the test succeeds because data 0X255 & 0X255 are copied to the beginning of data_ , from 0x7ffebcec1a60  to 0x7ffebcec1a61 , generating 65535.  
  
However, on AIX, I have:  
```  
 packed_channels565:  sizeof(struct packed_channels565) 32   &data_: ffffffffffff498  
  
........  
  
 set_data  &val:      ffffffffffff2f4   val:        65535  
     static_copy_bytes:  from: ffffffffffff2f4    0 0x 0   to: ffffffffffff498    0 0x 0 Bef  
     static_copy_bytes:  from: ffffffffffff2f4    0 0x 0   to: ffffffffffff498    0 0x 0 Aft  
     static_copy_bytes:  from: ffffffffffff2f5    0 0x 0   to: ffffffffffff499    0 0x 0 Bef  
     static_copy_bytes:  from: ffffffffffff2f5    0 0x 0   to: ffffffffffff499    0 0x 0 Aft  
     static_copy_bytes:  from: ffffffffffff2f6  255 0xff   to: ffffffffffff49a    7 0x 7 Bef  
     static_copy_bytes:  from: ffffffffffff2f6  255 0xff   to: ffffffffffff49a  255 0xff Aft  
     static_copy_bytes:  from: ffffffffffff2f7  255 0xff   to: ffffffffffff49b  255 0xff Bef  
     static_copy_bytes:  from: ffffffffffff2f7  255 0xff   to: ffffffffffff49b  255 0xff Aft  
 set_data  _data_ptr: ffffffffffff498   *_data_ptr: 65535  
test_packed_channels565:         &fix.data_: ffffffffffff498  &exp.max_v_: ffffffffffff4ba  
test_packed_channels565:          fix.data_: 0   exp.max_v_: 65535  
````  
Which shows that values on the left are copied to the second part of the 32bit word, to bytes ffffffffffff49a  and ffffffffffff49b, instead of  ffffffffffff498 and ffffffffffff499, where is "data_".  
And thus, its value is 0 and not 65535.  
  
There is some Little Endian thing that makes the algorithm to not start correctly in the correct half part of the 32bit short, thus coping values in the wrong half word pointed by data_ .  
However, for now, I've still not found which line of code is the root cause for this. However, it is probably in the following instructions:  
```  
void operator()(unsigned char const* from, unsigned char* to)  :   
static_copy_bytes<K - 1>()(++from, ++to);                   *to = *from;  
  
 const OutPtr gil_reinterpret_cast_c(const In* p)  
 {    return static_cast<const OutPtr>(static_cast<const void*>(p));   }  
  
get_data():   static_copy_bytes<sizeof(bitfield_t) >()(gil_reinterpret_cast_c<const unsigned char*>(_data_ptr),gil_reinterpret_cast<unsigned char*>(&ret));  
  
set_data(const bitfield_t& val):   static_copy_bytes<sizeof(bitfield_t) >()(gil_reinterpret_cast_c<const unsigned char*>(_data_ptr),gil_reinterpret_cast<unsigned char*>(&ret));  
  
void set_unsafe(integer_t value) :  this->set_data((this->get_data() & ~channel_mask) | (( static_cast< BitField >( value )<<FirstBit)));  
  
    packed_channel_reference const& operator=(integer_t value) const  
    {  
        set_unsafe(value);  
        return *this;  
    }  
  
```  
  
"On big endian platforms, the value 1 is stored in binary and is represented here in hexadecimal notation. One byte is stored as 01, two bytes as 00 01, and four bytes as 00 00 00 01. On little endian platforms, the value 1 is stored in one byte as 01 (the same as big endian), in two bytes as 01 00, and in four bytes as 01 00 00 00."  
  
OK. So, it seems that, on BigEndian machines, the integer "value" in operator= above must be converted to LittleEndian format so that the test works.
