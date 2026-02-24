# #43 - V2 input crash when offset is unspecified [Open]

> Username: ccorn  
> Created at: 2019-09-16 19:34:16 UTC  
> Updated at: 2020-01-28 21:32:13 UTC  
> Url: https://github.com/boostorg/chrono/issues/43  

The following simple program crashes (SIGSEGV) with Boost 1.71.0 when reaching `in >> tp`:  
  
~~~ c++  
#define BOOST_CHRONO_VERSION 2  
#include <boost/chrono.hpp>  
#include <boost/chrono/io/time_point_io.hpp>  
#include <cassert>      // assert  
#include <sstream>      // std::istringstream  
#include <string>       // std::string  
  
int main() {  
    namespace bc = boost::chrono;  
  
    std::istringstream in("2019-09-16 13:16:31.539");   // missing offset  
    // bc::timezone_io_saver tzs(in, bc::timezone::local); // try this as well  
    bc::system_clock::time_point tp;  
  
    in >> tp;           // crashes with default format  
    assert(in.eof());  
    assert(in.fail());  
    assert(!in.bad());  
    return 0;  
}  
~~~  
  
The following diff prevents the crash and fulfills the `assert`s:  
  
~~~ c++  
--- boost/chrono/io/time_point_io.hpp.orig	2019-08-14 14:02:21.000000000 +0200  
+++ boost/chrono/io/time_point_io.hpp	2019-09-16 20:13:47.435303802 +0200  
@@ -1152,6 +1152,11 @@ namespace boost  
             }  
             It i(is);  
             It eof;  
+            if (i == eof)  
+            {  
+              err |= std::ios_base::failbit;  
+              goto exit;  
+            }  
             c = *i;  
             if (++i == eof || c != ' ')  
             {  
~~~

---

## Comment 1

> Username: viboes  
> Created at: 2020-01-28 21:32:00 UTC  
> Url: https://github.com/boostorg/chrono/issues/43#issuecomment-579466086  

A PR is more than welcome.
