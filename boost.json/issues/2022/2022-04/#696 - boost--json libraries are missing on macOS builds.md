# #696 - boost::json libraries are missing on macOS builds [Closed]

> Username: hungptit  
> Created at: 2022-04-22 15:57:08 UTC  
> Updated at: 2022-04-22 17:38:46 UTC  
> Closed at: 2022-04-22 17:37:43 UTC  
> Url: https://github.com/boostorg/json/issues/696  

I could not install boost::json libraries on macOS using the b2 command (see the sample Bash command below). I have the `json.hpp` header file installed in the include folder.  
  
```text  
hungdang-ltm:boost-bjam-build hung.dang$ exa src/clang.osx10.11-x64/debug-x64/lib/ -l  
drwxr-xr-x    - hung.dang 22 Apr 11:25 cmake  
.rw-r--r-- 106k hung.dang 22 Apr 11:25 libboost_atomic-mt-d-x64.a  
.rwxr-xr-x  94k hung.dang 22 Apr 11:27 libboost_atomic-mt-d-x64.dylib  
.rw-r--r-- 496k hung.dang 22 Apr 11:25 libboost_chrono-d-x64.a  
.rwxr-xr-x 127k hung.dang 22 Apr 11:27 libboost_chrono-d-x64.dylib  
.rw-r--r-- 496k hung.dang 22 Apr 11:25 libboost_chrono-mt-d-x64.a  
.rwxr-xr-x 127k hung.dang 22 Apr 11:27 libboost_chrono-mt-d-x64.dylib  
.rw-r--r-- 415k hung.dang 22 Apr 11:25 libboost_container-d-x64.a  
.rwxr-xr-x 226k hung.dang 22 Apr 11:27 libboost_container-d-x64.dylib  
.rw-r--r-- 415k hung.dang 22 Apr 11:25 libboost_container-mt-d-x64.a  
.rwxr-xr-x 226k hung.dang 22 Apr 11:27 libboost_container-mt-d-x64.dylib  
.rw-r--r-- 7.7k hung.dang 22 Apr 11:25 libboost_date_time-d-x64.a  
.rwxr-xr-x  17k hung.dang 22 Apr 11:27 libboost_date_time-d-x64.dylib  
.rw-r--r-- 7.7k hung.dang 22 Apr 11:25 libboost_date_time-mt-d-x64.a  
.rwxr-xr-x  17k hung.dang 22 Apr 11:27 libboost_date_time-mt-d-x64.dylib  
.rw-r--r-- 1.9M hung.dang 22 Apr 11:25 libboost_filesystem-d-x64.a  
.rwxr-xr-x 454k hung.dang 22 Apr 11:27 libboost_filesystem-d-x64.dylib  
.rw-r--r-- 1.9M hung.dang 22 Apr 11:25 libboost_filesystem-mt-d-x64.a  
.rwxr-xr-x 455k hung.dang 22 Apr 11:27 libboost_filesystem-mt-d-x64.dylib  
.rw-r--r-- 1.0M hung.dang 22 Apr 11:25 libboost_iostreams-d-x64.a  
.rwxr-xr-x 246k hung.dang 22 Apr 11:27 libboost_iostreams-d-x64.dylib  
.rw-r--r-- 1.0M hung.dang 22 Apr 11:27 libboost_iostreams-mt-d-x64.a  
.rwxr-xr-x 246k hung.dang 22 Apr 11:27 libboost_iostreams-mt-d-x64.dylib  
.rw-r--r-- 7.6M hung.dang 22 Apr 11:25 libboost_program_options-d-x64.a  
.rwxr-xr-x 2.4M hung.dang 22 Apr 11:27 libboost_program_options-d-x64.dylib  
.rw-r--r-- 7.6M hung.dang 22 Apr 11:27 libboost_program_options-mt-d-x64.a  
.rwxr-xr-x 2.4M hung.dang 22 Apr 11:27 libboost_program_options-mt-d-x64.dylib  
.rw-r--r-- 3.3M hung.dang 22 Apr 11:25 libboost_regex-d-x64.a  
.rwxr-xr-x 1.4M hung.dang 22 Apr 11:27 libboost_regex-d-x64.dylib  
.rw-r--r-- 3.3M hung.dang 22 Apr 11:27 libboost_regex-mt-d-x64.a  
.rwxr-xr-x 1.4M hung.dang 22 Apr 11:27 libboost_regex-mt-d-x64.dylib  
.rw-r--r-- 1.0M hung.dang 22 Apr 11:25 libboost_thread-mt-d-x64.a  
.rwxr-xr-x 408k hung.dang 22 Apr 11:27 libboost_thread-mt-d-x64.dylib  
```  
  
### Version of Boost  
  
boost-1.77  
  
### Steps necessary to reproduce the problem  
  
Execute these commands at the root of the boost's source folder  
  
```shell  
boost_prefix="tmp"  
toolset="clang"  
number_of_cores="9"  
config="release"  
arch="64"  
  
./bootstrap.sh --prefix="$boost_prefix" --with-libraries=chrono,thread,regex,filesystem,date_time,program_options,atomic,container,json,regex --with-toolset="$toolset"  
./b2 clean  
  
libraries=(--with-chrono --with-thread --with-date_time --with-filesystem --with-program_options --with-json --with-iostreams --with-atomic --with-container --with-regex)  
    
build_options=(link=static link=shared threading=single threading=multi --layout=tagged --disable-icu)  
  
./b2 --prefix="$boost_prefix" --build-dir="$build_dir" -j"$number_of_cores" variant="$config" toolset="$toolset" address-model="$arch" "${build_options[@]}" "${libraries[@]}" install >/dev/null  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2022-04-22 16:00:44 UTC  
> Url: https://github.com/boostorg/json/issues/696#issuecomment-1106652602  

If the default `-std` level for your Clang is C++03, make sure to add an appropriate `cxxstd` option, such as `cxxstd=11` for C++11 or `cxxstd=14` for C++14. Boost.JSON requires C++11.

---

## Comment 2

> Username: grisumbras  
> Created at: 2022-04-22 17:20:01 UTC  
> Url: https://github.com/boostorg/json/issues/696#issuecomment-1106712026  

It would be great if you attached b2 output. In Boost 1.79 we print a diagnostic message if JSON is requested but skipped due to unmet requirements.

---

## Comment 3

> Username: hungptit  
> Created at: 2022-04-22 17:37:17 UTC  
> Updated at: 2022-04-22 17:37:39 UTC  
> Url: https://github.com/boostorg/json/issues/696#issuecomment-1106724091  

@pdimov  @grisumbras Thanks a lot for your suggestions. I can see the boost::json libraries after adding the `cxxstd=14` option.  
  
```text  
hungdang-ltm:boost-bjam-build hung.dang$ fast-find . -e "json.*(a|dylib)$"  
src/clang.osx10.11-x64/debug-x64/lib/libboost_json-mt-d-x64.dylib  
src/clang.osx10.11-x64/debug-x64/lib/libboost_json-d-x64.a  
src/clang.osx10.11-x64/debug-x64/lib/libboost_json-mt-d-x64.a  
src/clang.osx10.11-x64/debug-x64/lib/libboost_json-d-x64.dylib  
src/clang.osx10.11-x64/release-x32/lib/libboost_json-mt-x32.a  
src/clang.osx10.11-x64/release-x32/lib/libboost_json-x32.a  
src/clang.osx10.11-x64/debug-x32/lib/libboost_json-mt-d-x32.a  
src/clang.osx10.11-x64/debug-x32/lib/libboost_json-d-x32.a  
src/clang.osx10.11-x64/release-x64/lib/libboost_json-mt-x64.a  
src/clang.osx10.11-x64/release-x64/lib/libboost_json-mt-x64.dylib  
src/clang.osx10.11-x64/release-x64/lib/libboost_json-x64.a  
src/clang.osx10.11-x64/release-x64/lib/libboost_json-x64.dylib  
```
