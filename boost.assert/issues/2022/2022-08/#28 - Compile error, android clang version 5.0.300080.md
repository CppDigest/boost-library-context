# #28 - Compile error, android clang version 5.0.300080 [Closed]

> Username: zhuxiaoguang  
> Created at: 2022-08-24 08:49:46 UTC  
> Updated at: 2024-02-03 08:42:54 UTC  
> Closed at: 2024-02-03 08:42:54 UTC  
> Url: https://github.com/boostorg/assert/issues/28  

boost: 1.80.0  
android ndk: r16b  
clang version: 5.0.300080  
  
message:  
use of undeclared identifier '__builtin_FUNCTION'  
note: expanded from macro 'BOOST_CURRENT_LOCATION'  
# define BOOST_CURRENT_LOCATION ::boost::source_location(__builtin_FILE(), __builtin_LINE(), __builtin_FUNCTION(), __builtin_COLUMN())

---

## Comment 1

> Username: pdimov  
> Created at: 2022-08-24 16:17:08 UTC  
> Url: https://github.com/boostorg/assert/issues/28#issuecomment-1225944678  

This definition is only used for Clang 9.0 and above:  
  
https://github.com/boostorg/assert/blob/7dea14cf7f21dcd5bc5d4cedfd22935878634cdf/include/boost/assert/source_location.hpp#L168-L170  
  
If you Clang version is 5.0, you should check what's the value of the macro BOOST_CLANG_VERSION, and if it's higher than 90000, investigate why it's set to that. For Clang 5 it should be 50000 or thereabouts.  
  
https://github.com/boostorg/config/blob/acd45ca373b9044236aae8b708431c3c60c51bee/include/boost/config/compiler/clang_version.hpp#L7

---

## Comment 2

> Username: zhuxiaoguang  
> Created at: 2022-08-25 01:59:53 UTC  
> Url: https://github.com/boostorg/assert/issues/28#issuecomment-1226681204  

[root@0000]# arm-linux-androideabi-clang --version  
Android clang version 5.0.300080  (based on LLVM 5.0.300080)  
  
#define BOOST_CLANG_VERSION  (__clang_major__ * 10000 + __clang_minor__ * 100 + __clang_patchlevel__)  
__clang_major__ = 5  
__clang_minor__ = 0  
__clang_patchlevel__ = 300080  
  
BOOST_CLANG_VERSION = ( 5 * 10000 + 0 * 100 + 300080 ) = 350080

---

## Comment 3

> Username: pdimov  
> Created at: 2022-08-29 17:22:26 UTC  
> Url: https://github.com/boostorg/assert/issues/28#issuecomment-1230611531  

I have created a pull request against Boost.Config that should fix this issue: https://github.com/boostorg/config/pull/444  
The patch itself is https://github.com/boostorg/config/pull/444/commits/9eaeeb7844a748c1d34a276080e1aa26bc89012c. Please let me know if that works for you.
