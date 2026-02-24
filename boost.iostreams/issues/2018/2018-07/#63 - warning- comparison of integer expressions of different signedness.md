# #63 - warning: comparison of integer expressions of different signedness [Closed]

> Username: kivadiu  
> Created at: 2018-07-30 08:47:25 UTC  
> Updated at: 2018-08-30 11:42:26 UTC  
> Closed at: 2018-08-01 13:05:00 UTC  
> Url: https://github.com/boostorg/iostreams/issues/63  

While building 1.68.0 beta 1 on x86_64 linux cross-compiling to Windows 64 bits with mingw-w64 + g++ 8.2.0, I get this warning:  
```  
"x86_64-w64-mingw32-g++-8.2.0"   -I/softs/win64-mingw-8.2.0/release/iconv/include -I/softs/win64-mingw-8.2.0/release/gettext/include -I/softs/win64-mingw-8.2.0/release/bzip2/include -I/softs/win64-mingw-8.2.0/release/zlib/include -I/softs/win64-mingw-8.2.0/release/jpeg-turbo/include -I/softs/win64-mingw-8.2.0/release/xz/include -I/softs/win64-mingw-8.2.0/release/tiff/include -I/softs/win64-mingw-8.2.0/release/png/include -std=c++14 -O2 -DNDEBUG -m64 -mthreads -O3 -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_FILESYSTEM_NO_DEPRECATED -DBOOST_IOSTREAMS_USE_DEPRECATED -DBOOST_LOG_WITHOUT_EVENT_LOG -DNDEBUG  -I"." -I"/softs/win64-mingw-8.2.0/release/bzip2/include" -I"/softs/win64-mingw-8.2.0/release/zlib/include" -c -o "bin.v2/libs/iostreams/build/gcc-8.2.0/release/link-static/target-os-windows/threading-multi/mapped_file.o" "libs/iostreams/src/mapped_file.cpp"  
libs/iostreams/src/mapped_file.cpp: In member function 'void boost::iostreams::detail::mapped_file_impl::try_map_file(boost::iostreams::detail::mapped_file_impl::param_type)':  
libs/iostreams/src/mapped_file.cpp:324:29: warning: comparison of integer expressions of different signedness: 'boost::iostreams::stream_offset' {aka 'long long int'} and 'boost::iostreams::mapped_file_source::size_type' {aka 'long long unsigned int'} [-Wsign-compare]  
             (SIZE_T) (size_ != max_length ? size_ : 0),  
                       ~~~~~~^~~~~~~~~~~~~  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2018-07-30 13:14:41 UTC  
> Url: https://github.com/boostorg/iostreams/issues/63#issuecomment-408858769  

size_ is a stream_offset which is a `boost::intmax_t` which is signed  
max_length is a mapped_file_source::size_type which is a `std::size_t` which is unsigned  
  
Not certain where a stream_offset would be negative except for relative movement.  
Regardless, casting should resolve.  Can you submit a PR that resolves this?  
The CI build will check it.

---

## Comment 2

> Username: kivadiu  
> Created at: 2018-07-30 16:09:19 UTC  
> Url: https://github.com/boostorg/iostreams/issues/63#issuecomment-408919070  

Do you propose to cast the signed to unsigned then?  
I will make a PR.

---

## Comment 3

> Username: kivadiu  
> Created at: 2018-07-30 17:04:13 UTC  
> Url: https://github.com/boostorg/iostreams/issues/63#issuecomment-408937126  

PR ongoing CI: https://github.com/boostorg/iostreams/pull/64

---

## Comment 4

> Username: kivadiu  
> Created at: 2018-07-30 21:09:05 UTC  
> Url: https://github.com/boostorg/iostreams/issues/63#issuecomment-409011271  

CI passed.
