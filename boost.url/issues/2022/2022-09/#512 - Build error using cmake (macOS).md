# #512 - Build error using cmake (macOS) [Closed]

> Username: puremourning  
> Created at: 2022-09-07 14:38:02 UTC  
> Updated at: 2022-09-08 16:43:25 UTC  
> Closed at: 2022-09-08 16:43:25 UTC  
> Url: https://github.com/boostorg/url/issues/512  

I'm struggling to build the library, or incorporate it in my project. There seem to be a number of issues, so I must be doing something wrong, and would appreciate advice.  
  
I have tried to build the library as follows:  
  
## Everything  
  
1. clone this repo  
2. `cmake -S . -B build-output`  
  
Result:  
  
```  
ben@BeniMac2020 url % cmake -S . -B build-output  
-- The CXX compiler identification is AppleClang 13.1.6.13160021  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Found Boost: /usr/local/lib/cmake/Boost-1.79.0/BoostConfig.cmake (found suitable version "1.79.0", minimum required is "1.78.0") found components: container  
-- Configuring done  
CMake Error at test/unit/CMakeLists.txt:102 (add_executable):  
  Target "boost_url_tests" links to target "Boost::filesystem" but the target  
  was not found.  Perhaps a find_package() call is missing for an IMPORTED  
  target, or an ALIAS target is missing?  
  
  
CMake Error at test/unit/CMakeLists.txt:102 (add_executable):  
  Target "boost_url_tests" links to target "Boost::unordered" but the target  
  was not found.  Perhaps a find_package() call is missing for an IMPORTED  
  target, or an ALIAS target is missing?  
  
  
CMake Error at example/route/CMakeLists.txt:14 (add_executable):  
  Target "route" links to target "Boost::filesystem" but the target was not  
  found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
  an ALIAS target is missing?  
  
  
-- Generating done  
CMake Generate step failed.  Build files cannot be regenerated correctly.  
ben@BeniMac2020 url %  
```  
  
## Just the lib  
  
It seems the test and examples fail to configure, so I can disable them:  
  
```  
ben@BeniMac2020 url % cmake -S . -B build-output -DBOOST_URL_BUILD_TESTS=OFF -DBOOST_URL_BUILD_EXAMPLES=OFF  
-- The CXX compiler identification is AppleClang 13.1.6.13160021  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Found Boost: /usr/local/lib/cmake/Boost-1.79.0/BoostConfig.cmake (found suitable version "1.79.0", minimum required is "1.78.0") found components: container  
-- Configuring done  
-- Generating done  
-- Build files have been written to: /Users/ben/Development/YouCompleteMe/url/build-output  
ben@BeniMac2020 url % cmake --build build-output  
[ 50%] Building CXX object CMakeFiles/boost_url.dir/src/src.cpp.o  
In file included from /Users/ben/Development/YouCompleteMe/url/src/src.cpp:10:  
In file included from /Users/ben/Development/YouCompleteMe/url/include/boost/url/src.hpp:28:  
In file included from /Users/ben/Development/YouCompleteMe/url/include/boost/url.hpp:15:  
In file included from /Users/ben/Development/YouCompleteMe/url/include/boost/url/authority_view.hpp:17:  
/Users/ben/Development/YouCompleteMe/url/include/boost/url/pct_string_view.hpp:695:16: error: use of undeclared identifier 'hash_value'  
        return hash_value(s.s_);  
               ^  
1 error generated.  
make[2]: *** [CMakeFiles/boost_url.dir/src/src.cpp.o] Error 1  
make[1]: *** [CMakeFiles/boost_url.dir/all] Error 2  
make: *** [all] Error 2  
ben@BeniMac2020 url %  
```  
  
Environment:  
  
macOS 12.5.1 Intel  
  
clang:  
  
```  
ben@BeniMac2020 url % clang --version  
Apple clang version 13.1.6 (clang-1316.0.21.2.5)  
Target: x86_64-apple-darwin21.6.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
```  
  
revision   
  
```  
ben@BeniMac2020 url % git rev-parse HEAD  
0cbcbcd12a00b0ed3f2338cd0f8d365ca50dadaf  
```  
  
Including the source directly in my project leads to similar build errors, and the following trivial test does too:  
  
  
```  
ben@BeniMac2020 url % cat test.cc  
#include <boost/url.hpp>  
#include <boost/url/src.hpp>  
  
int main()  
{  
}  
ben@BeniMac2020 url % clang++ -std=c++17 -o test -isystem include test.cc  
In file included from test.cc:1:  
In file included from include/boost/url.hpp:15:  
In file included from include/boost/url/authority_view.hpp:17:  
include/boost/url/pct_string_view.hpp:695:16: error: use of undeclared identifier 'hash_value'  
        return hash_value(s.s_);  
               ^  
1 error generated.  
ben@BeniMac2020 url %  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-07 14:39:00 UTC  
> Url: https://github.com/boostorg/url/issues/512#issuecomment-1239480806  

Did you install the latest Boost?

---

## Comment 2

> Username: puremourning  
> Created at: 2022-09-07 14:39:50 UTC  
> Url: https://github.com/boostorg/url/issues/512#issuecomment-1239481890  

I have boost 1.79.0  
  
```  
ben@BeniMac2020 url % brew info boost  
==> boost: stable 1.79.0 (bottled), HEAD  
Collection of portable C++ source libraries  
https://www.boost.org/  
/usr/local/Cellar/boost/1.79.0_2 (15,462 files, 464.5MB) *  
  Poured from bottle on 2022-09-07 at 14:52:15  
From: https://github.com/Homebrew/homebrew-core/blob/HEAD/Formula/boost.rb  
License: BSL-1.0  
==> Dependencies  
Required: icu4c ✔, xz ✔, zstd ✔  
==> Options  
--HEAD  
	Install HEAD version  
==> Analytics  
install: 112,320 (30 days), 353,702 (90 days), 1,077,028 (365 days)  
install-on-request: 26,259 (30 days), 83,227 (90 days), 244,644 (365 days)  
build-error: 27 (30 days)  
ben@BeniMac2020 url %  
```

---

## Comment 3

> Username: puremourning  
> Created at: 2022-09-07 16:43:37 UTC  
> Url: https://github.com/boostorg/url/issues/512#issuecomment-1239635550  

If it helps, the build error from my actual project has more verbosity:  
  
```  
/Users/ben/Development/YouCompleteMe/ycmd-cpp/vendor/boost-url/include/boost/url/pct_string_view.hpp:658:16: error: no matching  
      function for call to 'hash_value'  
        return hash_value(s.s_);  
               ^~~~~~~~~~  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:526:24: note:   
      candidate function not viable: no known conversion from 'const boost::urls::string_view' (aka 'const basic_string_view<char>') to 'std::monostate'  
      for 1st argument  
    inline std::size_t hash_value(std::monostate) {  
                       ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:541:24: note:   
      candidate function not viable: no known conversion from 'const boost::urls::string_view' (aka 'const basic_string_view<char>') to 'std::type_index'  
      for 1st argument  
    inline std::size_t hash_value(std::type_index v)  
                       ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:548:24: note:   
      candidate function not viable: no known conversion from 'const boost::urls::string_view' (aka 'const basic_string_view<char>') to  
      'const std::error_code' for 1st argument  
    inline std::size_t hash_value(std::error_code const& v) {  
                       ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:555:24: note:   
      candidate function not viable: no known conversion from 'const boost::urls::string_view' (aka 'const basic_string_view<char>') to  
      'const std::error_condition' for 1st argument  
    inline std::size_t hash_value(std::error_condition const& v) {  
                       ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/smart_ptr/shared_ptr.hpp:1218:33: note:   
      candidate template ignored: could not match 'shared_ptr' against 'basic_string_view'  
template< class T > std::size_t hash_value( boost::shared_ptr<T> const & p ) BOOST_SP_NOEXCEPT  
                                ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/smart_ptr/weak_ptr.hpp:291:33: note:   
      candidate template ignored: could not match 'weak_ptr' against 'basic_string_view'  
template< class T > std::size_t hash_value( boost::weak_ptr<T> const & p ) BOOST_SP_NOEXCEPT  
                                ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/utility/string_view.hpp:658:17: note:   
      candidate template ignored: could not match 'boost::basic_string_view' against 'boost::core::basic_string_view'  
    std::size_t hash_value(basic_string_view<charT, traits> s) {  
                ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:364:57: note:   
      candidate template ignored: substitution failure [with T = boost::core::basic_string_view<char>]: no type named 'type' in  
      'boost::hash_detail::basic_numbers<boost::core::basic_string_view<char>>'  
    typename boost::hash_detail::basic_numbers<T>::type hash_value(T v)  
                                                   ~~~~ ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:370:56: note:   
      candidate template ignored: substitution failure [with T = boost::core::basic_string_view<char>]: no type named 'type' in  
      'boost::hash_detail::long_numbers<boost::core::basic_string_view<char>>'  
    typename boost::hash_detail::long_numbers<T>::type hash_value(T v)  
                                                  ~~~~ ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:376:57: note:   
      candidate template ignored: substitution failure [with T = boost::core::basic_string_view<char>]: no type named 'type' in  
      'boost::hash_detail::ulong_numbers<boost::core::basic_string_view<char>>'  
    typename boost::hash_detail::ulong_numbers<T>::type hash_value(T v)  
                                                   ~~~~ ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:382:31: note:   
      candidate template ignored: disabled by 'enable_if' [with T = boost::core::basic_string_view<char>]  
    typename boost::enable_if<boost::is_enum<T>, std::size_t>::type  
                              ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:390:36: note:   
      candidate template ignored: could not match 'T *const' against 'const boost::urls::string_view' (aka 'const basic_string_view<char>')  
    template <class T> std::size_t hash_value(T* const& v)  
                                   ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:478:24: note:   
      candidate template ignored: could not match 'const T [N]' against 'const boost::urls::string_view' (aka 'const basic_string_view<char>')  
    inline std::size_t hash_value(const T (&x)[N])  
                       ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:484:24: note:   
      candidate template ignored: could not match 'T [N]' against 'const boost::urls::string_view' (aka 'const basic_string_view<char>')  
    inline std::size_t hash_value(T (&x)[N])  
                       ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:491:24: note:   
      candidate template ignored: could not match 'basic_string' against 'basic_string_view'  
    inline std::size_t hash_value(  
                       ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:499:24: note:   
      candidate template ignored: could not match 'std::basic_string_view' against 'boost::core::basic_string_view'  
    inline std::size_t hash_value(  
                       ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:507:57: note:   
      candidate template ignored: substitution failure [with T = boost::core::basic_string_view<char>]: no type named 'type' in  
      'boost::hash_detail::float_numbers<boost::core::basic_string_view<char>>'  
    typename boost::hash_detail::float_numbers<T>::type hash_value(T v)  
                                                   ~~~~ ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:514:24: note:   
      candidate template ignored: could not match 'optional' against 'basic_string_view'  
    inline std::size_t hash_value(std::optional<T> const& v) {  
                       ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/hash.hpp:531:24: note:   
      candidate template ignored: could not match 'variant' against 'basic_string_view'  
    inline std::size_t hash_value(std::variant<Types...> const& v) {  
                       ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/extensions.hpp:63:17: note:   
      candidate template ignored: could not match 'pair' against 'basic_string_view'  
    std::size_t hash_value(std::pair<A, B> const& v)  
                ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/extensions.hpp:72:17: note:   
      candidate template ignored: could not match 'vector' against 'basic_string_view'  
    std::size_t hash_value(std::vector<T, A> const& v)  
                ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/extensions.hpp:78:17: note:   
      candidate template ignored: could not match 'list' against 'basic_string_view'  
    std::size_t hash_value(std::list<T, A> const& v)  
                ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/extensions.hpp:84:17: note:   
      candidate template ignored: could not match 'deque' against 'basic_string_view'  
    std::size_t hash_value(std::deque<T, A> const& v)  
                ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/extensions.hpp:90:17: note:   
      candidate template ignored: could not match 'set' against 'basic_string_view'  
    std::size_t hash_value(std::set<K, C, A> const& v)  
                ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/extensions.hpp:96:17: note:   
      candidate template ignored: could not match 'multiset' against 'basic_string_view'  
    std::size_t hash_value(std::multiset<K, C, A> const& v)  
                ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/extensions.hpp:102:17: note:   
      candidate template ignored: could not match 'map' against 'basic_string_view'  
    std::size_t hash_value(std::map<K, T, C, A> const& v)  
                ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/extensions.hpp:108:17: note:   
      candidate template ignored: could not match 'multimap' against 'basic_string_view'  
    std::size_t hash_value(std::multimap<K, T, C, A> const& v)  
                ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/extensions.hpp:114:17: note:   
      candidate template ignored: could not match 'complex' against 'basic_string_view'  
    std::size_t hash_value(std::complex<T> const& v)  
                ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/extensions.hpp:124:17: note:   
      candidate template ignored: could not match 'array' against 'basic_string_view'  
    std::size_t hash_value(std::array<T, N> const& v)  
                ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/extensions.hpp:159:24: note:   
      candidate template ignored: could not match 'tuple' against 'basic_string_view'  
    inline std::size_t hash_value(std::tuple<T...> const& v)  
                       ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/extensions.hpp:236:24: note:   
      candidate template ignored: could not match 'shared_ptr' against 'basic_string_view'  
    inline std::size_t hash_value(std::shared_ptr<T> const& x) {  
                       ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/container_hash/extensions.hpp:241:24: note:   
      candidate template ignored: could not match 'unique_ptr' against 'basic_string_view'  
    inline std::size_t hash_value(std::unique_ptr<T, Deleter> const& x) {  
                       ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/smart_ptr/intrusive_ptr.hpp:381:33: note:   
      candidate template ignored: could not match 'intrusive_ptr' against 'basic_string_view'  
template< class T > std::size_t hash_value( boost::intrusive_ptr<T> const & p ) BOOST_SP_NOEXCEPT  
                                ^  
/Users/ben/.conan/data/boost/1.79.0/_/_/package/383bfa2443c96c8d22700cf3c7c30015af5694a5/include/boost/array.hpp:416:17: note:   
      candidate template ignored: could not match 'array' against 'basic_string_view'  
    std::size_t hash_value(const array<T,N>& arr)  
                ^  
1 error generated.  
make[3]: *** [src/CMakeFiles/ycmd.dir/ycmd.cpp.o] Error 1  
make[2]: *** [src/CMakeFiles/ycmd.dir/all] Error 2  
make[1]: *** [all] Error 2  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-09-08 16:24:59 UTC  
> Updated at: 2022-09-08 16:26:36 UTC  
> Url: https://github.com/boostorg/url/issues/512#issuecomment-1240942920  

You definitely will need at least Boost 1.80. And when Boost.URL becomes part of the official Boost distribution you will need 1.81 or later.

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-09-08 16:26:34 UTC  
> Url: https://github.com/boostorg/url/issues/512#issuecomment-1240944570  

@puremourning, this library uses a feature that's only available in Boost 1.80.

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-09-08 16:28:53 UTC  
> Url: https://github.com/boostorg/url/issues/512#issuecomment-1240946908  

Since the library has been accepted, it just depends on the most recent version of boost. We have updated the documentation to reflect that in https://github.com/CPPAlliance/url/pull/513. But Boost 1.80 will do.

---

## Comment 7

> Username: puremourning  
> Created at: 2022-09-08 16:43:25 UTC  
> Url: https://github.com/boostorg/url/issues/512#issuecomment-1240962778  

OK, thanks for the info.
