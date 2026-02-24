# #106 - `error: cannot bind non-const lvalue reference of type 'boost::system::error_code&' to an rvalue of type 'boost::system::error_code'` [Closed]

> Username: 0-wiz-0  
> Created at: 2025-10-21 07:53:17 UTC  
> Updated at: 2025-10-24 16:12:19 UTC  
> Closed at: 2025-10-24 16:12:19 UTC  
> Url: https://github.com/boostorg/dll/issues/106  

After the boost update to 1.89 in pkgsrc, a couple programs stopped compiling. One new error is [this one](https://releng.netbsd.org/pkgreports/shadow/HEAD/NetBSD-11.0-x86_64/20251020.1455/uhd-4.8.0.0nb2/build.log):  
```  
In file included from /pbulk/work/ham/uhd/work/.buildlink/include/boost/dll/runtime_symbol_info.hpp:35,  
                 from /pbulk/work/ham/uhd/work/uhd-4.8.0.0/host/lib/utils/paths.cpp:15:  
/pbulk/work/ham/uhd/work/.buildlink/include/boost/dll/detail/posix/program_location_impl.hpp: In function 'boost::filesystem::path boost::dll::detail::program_location_impl(std::error_code&)':  
/pbulk/work/ham/uhd/work/.buildlink/include/boost/dll/detail/posix/program_location_impl.hpp:103:66: error: cannot bind non-const lvalue reference of type 'boost::system::error_code&' to an rvalue of type 'boost::system::error_code'  
  103 |         return boost::dll::fs::read_symlink("/proc/curproc/exe", ec);  
      |                                                                  ^~  
```  
  
Comparing the relevant code:  
```  
#elif BOOST_OS_BSD_NET  
  
namespace boost { namespace dll { namespace detail {  
    inline boost::dll::fs::path program_location_impl(std::error_code &ec) {  
        return boost::dll::fs::read_symlink("/proc/curproc/exe", ec);  
    }  
}}} // namespace boost::dll::detail  
  
```  
with code further down in the same file:  
```  
#else  // BOOST_OS_LINUX || BOOST_OS_UNIX || BOOST_OS_HPUX || BOOST_OS_ANDROID  
  
namespace boost { namespace dll { namespace detail {  
    inline boost::dll::fs::path program_location_impl(std::error_code &ec) {  
        // We can not use  
        // boost::dll::detail::path_from_handle(dlopen(NULL, RTLD_LAZY | RTLD_LOCAL), ignore);  
        // because such code returns empty path.  
  
        boost::dll::fs::error_code fs_errc;  
        auto result = boost::dll::fs::read_symlink("/proc/self/exe", fs_errc);   // Linux specific  
        ec = fs_errc;  
        return result;  
    }  
}}} // namespace boost::dll::detail  
  
#endif  
```  
I came up with this patch:  
```  
--- boost/dll/detail/posix/program_location_impl.hpp.orig       2025-10-21 07:45:26.595305483 +0000  
+++ boost/dll/detail/posix/program_location_impl.hpp  
@@ -100,7 +100,10 @@ namespace boost { namespace dll { namesp  
  
 namespace boost { namespace dll { namespace detail {  
     inline boost::dll::fs::path program_location_impl(std::error_code &ec) {  
-        return boost::dll::fs::read_symlink("/proc/curproc/exe", ec);  
+        boost::dll::fs::error_code fs_errc;  
+        auto result = boost::dll::fs::read_symlink("/proc/curproc/exe", fs_errc);  
+        ec = fs_errc;  
+        return result;  
     }  
 }}} // namespace boost::dll::detail  
  
```  
which fixes the problem on my system.
