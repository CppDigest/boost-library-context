# #37 - boost::get_error_info() fails to lookup info for boost::stacktrace tag [Closed]

> Username: daverigby  
> Created at: 2021-02-10 15:45:46 UTC  
> Updated at: 2021-02-12 00:03:23 UTC  
> Closed at: 2021-02-12 00:03:23 UTC  
> Url: https://github.com/boostorg/exception/issues/37  

### Summary  
  
On macOS Catalina when throwing a `boost::enable_error_info` annotated exception from a shared library, then catching it from the main executable, and built with default symbol visibility to “hidden”, `get_error_info()` from main exe fails to locate exception details.  
  
### Steps to reproduce  
  
See reproducer at https://github.com/daverigby/boost_error_info_visibility  
  
### Analysis  
  
Thanks to @pdimov on #boost cpplang slack, we identified that a number of symbols in exception / stacktrace need `BOOST_SYMBOL_VISIBLE` applying. In my local Boost 1.75 world adding to the forward-decl and defns of _all_ of the following was required to make the test pass:  
  
* `boost::error_info`  
* `boost::error_info_base`  
* Tag template param for `boost::error_info`  
* "T" tempalate param for `boost_error_info` (which is boost_stacktrace and template params in my case):  
  * `boost::stacktrace::frame`  
  * `boost::basic_stacktrace`

---

## Comment 1

> Username: daverigby  
> Created at: 2021-02-10 16:03:13 UTC  
> Updated at: 2021-02-10 16:03:40 UTC  
> Url: https://github.com/boostorg/exception/issues/37#issuecomment-776816381  

Note: Adding the following forward-decls to the above testcase is sufficient to workaround the issue:  
  
```C++  
#define PUBLIC_API __attribute__((visibility("default")))  
  
namespace boost {  
template <class Tag, class T>  
class PUBLIC_API error_info;  
};  
namespace boost { namespace exception_detail {  
class PUBLIC_API error_info_base;  
};  
};  
namespace boost { namespace stacktrace {  
class PUBLIC_API frame;  
template <class Allocator> class PUBLIC_API basic_stacktrace;  
};  
};  
```  
  
Testcase updated to include this (commented out).

---

## Comment 2

> Username: daverigby  
> Created at: 2021-02-11 10:01:53 UTC  
> Updated at: 2021-02-11 10:02:03 UTC  
> Url: https://github.com/boostorg/exception/issues/37#issuecomment-777328949  

Applied changes from `feature/symbol_visibility_test` to my local Boost 1.75 via homebewl (I don't have build of boost from source):  
  
```  
git diff origin/develop..origin/feature/symbol_visibility_test include/boost/exception > ~/scratch/boost_exception_strcmp.patch  
cd /usr/local/include/boost/exception && patch -p4 < ~/scratch/boost_exception_strcmp.patch  
```  
  
This now passes the above testcase 👍

---

## Comment 3

> Username: zajo  
> Created at: 2021-02-12 00:03:23 UTC  
> Url: https://github.com/boostorg/exception/issues/37#issuecomment-777877015  

Merged into develop.
