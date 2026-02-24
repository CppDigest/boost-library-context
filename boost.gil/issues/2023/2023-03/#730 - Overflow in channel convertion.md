# #730 - Overflow in channel convertion [Open]

> Username: tanguimorvan  
> Created at: 2023-03-14 11:53:31 UTC  
> Updated at: 2023-03-14 14:30:12 UTC  
> Url: https://github.com/boostorg/gil/issues/730  

### Actual behavior  
  
Channel conversion from boost::gil::packed_channel_value to uint16_t fails due to integer overflow in channel_converter_unsigned_integral_nondivisible<SrcChannelV,DstChannelV,true,false> .  
  
  
### Expected  behavior  
  
The value should not overflow, producing correct results.  
  
### C++ Minimal Working Example  
  
```cpp  
#include <boost/gil.hpp>  
namespace gil = boost::gil;  
int main  
{  
    boost::gil::packed_channel_value<10> packedChannelValue(3);  
    //Expected 192, got 64  
    uint16_t convertedValue = boost::gil::channel_convert<uint16_t>(packedChannelValue);  
}  
```  
  
### Environment  
  
- Compiler version: GCC 8.4.0  
- Build settings:   
- Version (Git ref or `<boost/version.hpp>`): 1.68 but seems to still be present in latest version.

---

## Comment 1

> Username: tanguimorvan  
> Created at: 2023-03-14 11:57:08 UTC  
> Updated at: 2023-03-14 14:29:43 UTC  
> Url: https://github.com/boostorg/gil/issues/730#issuecomment-1467964646  

```  
Index: channel_algorithm.hpp  
===================================================================  
--- channel_algorithm.hpp	(revision 56209)  
+++ channel_algorithm.hpp	(working copy)  
@@ -237,7 +237,7 @@  
 struct channel_converter_unsigned_integral_nondivisible<SrcChannelV,DstChannelV,true,false> {  
     DstChannelV operator()(SrcChannelV src) const {  
         typedef typename base_channel_type<DstChannelV>::type dest_t;  
-        return DstChannelV(static_cast<dest_t>( src * unsigned_integral_max_value<DstChannelV>::value) / unsigned_integral_max_value<SrcChannelV>::value);  
+        return DstChannelV(static_cast<dest_t>( src ) * unsigned_integral_max_value<DstChannelV>::value / unsigned_integral_max_value<SrcChannelV>::value);  
     }  
 };  
 ```

---

## Comment 2

> Username: mloskot  
> Created at: 2023-03-14 14:30:11 UTC  
> Url: https://github.com/boostorg/gil/issues/730#issuecomment-1468208362  

@tanguimorvan Thanks, feel free to open PR with your changes
