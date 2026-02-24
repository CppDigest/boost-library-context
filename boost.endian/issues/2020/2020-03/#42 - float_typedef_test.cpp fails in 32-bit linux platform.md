# #42 - float_typedef_test.cpp fails in 32-bit linux platform [Closed]

> Username: yuxianch  
> Created at: 2020-03-11 07:15:44 UTC  
> Updated at: 2020-05-03 02:20:33 UTC  
> Closed at: 2020-05-03 02:20:33 UTC  
> Url: https://github.com/boostorg/endian/issues/42  

float_typedef_test.cpp fails in 32-bit linux platform. Line 24 in float_typedef_test.cpp works only for 64-bit target when class `big_float64_buf_at` or `little_float64_buf_at` are passed to function test_buffer.   
  
> BOOST_TEST_EQ( sizeof(x), aligned? 2 * sizeof(U): 1 + sizeof(U) );  
  
In 32-bit target, this code should be updated for the size of char+double is 12 bytes, not 2*8=16 bytes.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-03-13 00:24:46 UTC  
> Url: https://github.com/boostorg/endian/issues/42#issuecomment-598489147  

Thanks, should be fixed by https://github.com/boostorg/endian/commit/2b101c64a91e16800b6ef55fc8cadd3ad04a7af6.
