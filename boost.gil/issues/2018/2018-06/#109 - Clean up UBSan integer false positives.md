# #109 - Clean up UBSan integer false positives [Open]

> Username: mloskot  
> Created at: 2018-06-25 20:19:11 UTC  
> Updated at: 2018-06-25 20:19:58 UTC  
> Url: https://github.com/boostorg/gil/issues/109  

This is #108 follow-up.  
  
### Problem  
  
For example, `variant=ubsan_integer` failures like in [build 375.9](https://travis-ci.org/boostorg/gil/jobs/396282494#L1181), may be revealing some issues regarding actual unsigned type used in conversion routines.  
  
The following UBSan integer runtime error  
  
```  
channel_algorithm.hpp:344:64:  
  runtime error: unsigned integer overflow:  
    4294967287 + 128 cannot be represented in type 'unsigned int'  
```  
  
It is reported for `channel_convert_to_unsigned` utility  
  
https://github.com/boostorg/gil/blob/d882b844944001f5f94e55aed3faaf651aae3032/include/boost/gil/channel_algorithm.hpp#L339-L346  
  
For `val = -9` input, the result is calculated as:  
  
```  
((4294967295 - 8) + 128) % 256 = 119  
```  
  
Obviously, changing the intermediate conversion to  
`static_cast<uint16_t>(val)`  
or  
`static_cast<uint64_t>(val)`  
yield equivalent results:  
  
```  
((65535 - 8) +128) % 256 = 119  
((18446744073709551615 - 8) + 128) % 256 = 119  
```  
  
However, it may be a good idea to inspect all the `ubsan_integer` failures for any unexpected results.
