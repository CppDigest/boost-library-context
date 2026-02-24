# #774 - Boost 1.80 build failure with gcc11/gcc12: json/impl/parse.ipp: error: no match for 'operator='  (no such error with 1.79) [Closed]

> Username: barracuda156  
> Created at: 2022-10-18 14:44:14 UTC  
> Updated at: 2023-07-17 16:03:47 UTC  
> Closed at: 2023-07-17 15:48:30 UTC  
> Url: https://github.com/boostorg/json/issues/774  

```  
:info:build darwin.compile.c++ bin.v2/libs/json/build/darwin-12.2.0/release/threading-multi/visibility-hidden/src.o  
:info:build     "/opt/local/bin/g++-mp-12"   -fvisibility-inlines-hidden -Os -std=gnu++11 -D_GLIBCXX_USE_CXX11_ABI=0 -arch ppc  -fPIC -m32 -O3 -Wall -fvisibility=hidden -dynamic -gdwarf-2 -fexceptions -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_DYN_LINK=1 -DBOOST_JSON_DYN_LINK=1 -DBOOST_JSON_SOURCE -DNDEBUG  -I"."  -c -o "bin.v2/libs/json/build/darwin-12.2.0/release/threading-multi/visibility-hidden/src.o" "libs/json/src/src.cpp"  
:info:build In file included from ./boost/json/src.hpp:37,  
:info:build                  from libs/json/src/src.cpp:10:  
:info:build ./boost/json/impl/parse.ipp: In function 'boost::json::value boost::json::parse(string_view, std::error_code&, storage_ptr, const parse_options&)':  
:info:build ./boost/json/impl/parse.ipp:46:10: error: no match for 'operator=' (operand types are 'std::error_code' and 'boost::json::error_code' {aka 'boost::system::error_code'})  
:info:build    46 |     ec = jec;  
:info:build       |          ^~~  
:info:build In file included from /opt/local/include/gcc12/c++/bits/ios_base.h:46,  
:info:build                  from /opt/local/include/gcc12/c++/ios:42,  
:info:build                  from ./boost/core/detail/string_view.hpp:30,  
:info:build                  from ./boost/json/string_view.hpp:14,  
:info:build                  from ./boost/json/kind.hpp:14,  
:info:build                  from ./boost/json/array.hpp:14,  
:info:build                  from ./boost/json.hpp:15,  
:info:build                  from ./boost/json/src.hpp:27:  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: note: candidate: 'template<class _ErrorCodeEnum> typename std::enable_if<std::is_error_code_enum<_Tp>::value, std::error_code&>::type std::error_code::operator=(_ErrorCodeEnum)'  
:info:build   225 |       operator=(_ErrorCodeEnum __e) noexcept  
:info:build       |       ^~~~~~~~  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: note:   template argument deduction/substitution failed:  
:info:build /opt/local/include/gcc12/c++/system_error: In substitution of 'template<class _ErrorCodeEnum> typename std::enable_if<std::is_error_code_enum<_Tp>::value, std::error_code&>::type std::error_code::operator=(_ErrorCodeEnum) [with _ErrorCodeEnum = boost::system::error_code]':  
:info:build ./boost/json/impl/parse.ipp:46:10:   required from here  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: error: no type named 'type' in 'struct std::enable_if<false, std::error_code&>'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note: candidate: 'std::error_code& std::error_code::operator=(const std::error_code&)'  
:info:build   196 |   class error_code  
:info:build       |         ^~~~~~~~~~  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note:   no known conversion for argument 1 from 'boost::json::error_code' {aka 'boost::system::error_code'} to 'const std::error_code&'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note: candidate: 'std::error_code& std::error_code::operator=(std::error_code&&)'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note:   no known conversion for argument 1 from 'boost::json::error_code' {aka 'boost::system::error_code'} to 'std::error_code&&'  
:info:build In file included from ./boost/json/src.hpp:38:  
:info:build ./boost/json/impl/parser.ipp: In member function 'std::size_t boost::json::parser::write_some(const char*, std::size_t, std::error_code&)':  
:info:build ./boost/json/impl/parser.ipp:80:10: error: no match for 'operator=' (operand types are 'std::error_code' and 'boost::json::error_code' {aka 'boost::system::error_code'})  
:info:build    80 |     ec = jec;  
:info:build       |          ^~~  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: note: candidate: 'template<class _ErrorCodeEnum> typename std::enable_if<std::is_error_code_enum<_Tp>::value, std::error_code&>::type std::error_code::operator=(_ErrorCodeEnum)'  
:info:build   225 |       operator=(_ErrorCodeEnum __e) noexcept  
:info:build       |       ^~~~~~~~  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: note:   template argument deduction/substitution failed:  
:info:build /opt/local/include/gcc12/c++/system_error: In substitution of 'template<class _ErrorCodeEnum> typename std::enable_if<std::is_error_code_enum<_Tp>::value, std::error_code&>::type std::error_code::operator=(_ErrorCodeEnum) [with _ErrorCodeEnum = boost::system::error_code]':  
:info:build ./boost/json/impl/parser.ipp:80:10:   required from here  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: error: no type named 'type' in 'struct std::enable_if<false, std::error_code&>'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note: candidate: 'std::error_code& std::error_code::operator=(const std::error_code&)'  
:info:build   196 |   class error_code  
:info:build       |         ^~~~~~~~~~  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note:   no known conversion for argument 1 from 'boost::json::error_code' {aka 'boost::system::error_code'} to 'const std::error_code&'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note: candidate: 'std::error_code& std::error_code::operator=(std::error_code&&)'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note:   no known conversion for argument 1 from 'boost::json::error_code' {aka 'boost::system::error_code'} to 'std::error_code&&'  
:info:build ./boost/json/impl/parser.ipp: In member function 'std::size_t boost::json::parser::write(const char*, std::size_t, std::error_code&)':  
:info:build ./boost/json/impl/parser.ipp:125:10: error: no match for 'operator=' (operand types are 'std::error_code' and 'boost::json::error_code' {aka 'boost::system::error_code'})  
:info:build   125 |     ec = jec;  
:info:build       |          ^~~  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: note: candidate: 'template<class _ErrorCodeEnum> typename std::enable_if<std::is_error_code_enum<_Tp>::value, std::error_code&>::type std::error_code::operator=(_ErrorCodeEnum)'  
:info:build   225 |       operator=(_ErrorCodeEnum __e) noexcept  
:info:build       |       ^~~~~~~~  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: note:   template argument deduction/substitution failed:  
:info:build /opt/local/include/gcc12/c++/system_error: In substitution of 'template<class _ErrorCodeEnum> typename std::enable_if<std::is_error_code_enum<_Tp>::value, std::error_code&>::type std::error_code::operator=(_ErrorCodeEnum) [with _ErrorCodeEnum = boost::system::error_code]':  
:info:build ./boost/json/impl/parser.ipp:125:10:   required from here  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: error: no type named 'type' in 'struct std::enable_if<false, std::error_code&>'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note: candidate: 'std::error_code& std::error_code::operator=(const std::error_code&)'  
:info:build   196 |   class error_code  
:info:build       |         ^~~~~~~~~~  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note:   no known conversion for argument 1 from 'boost::json::error_code' {aka 'boost::system::error_code'} to 'const std::error_code&'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note: candidate: 'std::error_code& std::error_code::operator=(std::error_code&&)'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note:   no known conversion for argument 1 from 'boost::json::error_code' {aka 'boost::system::error_code'} to 'std::error_code&&'  
:info:build In file included from ./boost/json/src.hpp:39:  
:info:build ./boost/json/impl/pointer.ipp: In member function 'const boost::json::value* boost::json::value::find_pointer(boost::json::string_view, std::error_code&) const':  
:info:build ./boost/json/impl/pointer.ipp:328:10: error: no match for 'operator=' (operand types are 'std::error_code' and 'boost::json::error_code' {aka 'boost::system::error_code'})  
:info:build   328 |     ec = jec;  
:info:build       |          ^~~  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: note: candidate: 'template<class _ErrorCodeEnum> typename std::enable_if<std::is_error_code_enum<_Tp>::value, std::error_code&>::type std::error_code::operator=(_ErrorCodeEnum)'  
:info:build   225 |       operator=(_ErrorCodeEnum __e) noexcept  
:info:build       |       ^~~~~~~~  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: note:   template argument deduction/substitution failed:  
:info:build /opt/local/include/gcc12/c++/system_error: In substitution of 'template<class _ErrorCodeEnum> typename std::enable_if<std::is_error_code_enum<_Tp>::value, std::error_code&>::type std::error_code::operator=(_ErrorCodeEnum) [with _ErrorCodeEnum = boost::system::error_code]':  
:info:build ./boost/json/impl/pointer.ipp:328:10:   required from here  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: error: no type named 'type' in 'struct std::enable_if<false, std::error_code&>'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note: candidate: 'std::error_code& std::error_code::operator=(const std::error_code&)'  
:info:build   196 |   class error_code  
:info:build       |         ^~~~~~~~~~  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note:   no known conversion for argument 1 from 'boost::json::error_code' {aka 'boost::system::error_code'} to 'const std::error_code&'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note: candidate: 'std::error_code& std::error_code::operator=(std::error_code&&)'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note:   no known conversion for argument 1 from 'boost::json::error_code' {aka 'boost::system::error_code'} to 'std::error_code&&'  
:info:build In file included from ./boost/json/src.hpp:43:  
:info:build ./boost/json/impl/stream_parser.ipp: In member function 'std::size_t boost::json::stream_parser::write_some(const char*, std::size_t, std::error_code&)':  
:info:build ./boost/json/impl/stream_parser.ipp:78:10: error: no match for 'operator=' (operand types are 'std::error_code' and 'boost::json::error_code' {aka 'boost::system::error_code'})  
:info:build    78 |     ec = jec;  
:info:build       |          ^~~  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: note: candidate: 'template<class _ErrorCodeEnum> typename std::enable_if<std::is_error_code_enum<_Tp>::value, std::error_code&>::type std::error_code::operator=(_ErrorCodeEnum)'  
:info:build   225 |       operator=(_ErrorCodeEnum __e) noexcept  
:info:build       |       ^~~~~~~~  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: note:   template argument deduction/substitution failed:  
:info:build /opt/local/include/gcc12/c++/system_error: In substitution of 'template<class _ErrorCodeEnum> typename std::enable_if<std::is_error_code_enum<_Tp>::value, std::error_code&>::type std::error_code::operator=(_ErrorCodeEnum) [with _ErrorCodeEnum = boost::system::error_code]':  
:info:build ./boost/json/impl/stream_parser.ipp:78:10:   required from here  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: error: no type named 'type' in 'struct std::enable_if<false, std::error_code&>'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note: candidate: 'std::error_code& std::error_code::operator=(const std::error_code&)'  
:info:build   196 |   class error_code  
:info:build       |         ^~~~~~~~~~  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note:   no known conversion for argument 1 from 'boost::json::error_code' {aka 'boost::system::error_code'} to 'const std::error_code&'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note: candidate: 'std::error_code& std::error_code::operator=(std::error_code&&)'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note:   no known conversion for argument 1 from 'boost::json::error_code' {aka 'boost::system::error_code'} to 'std::error_code&&'  
:info:build ./boost/json/impl/stream_parser.ipp: In member function 'std::size_t boost::json::stream_parser::write(const char*, std::size_t, std::error_code&)':  
:info:build ./boost/json/impl/stream_parser.ipp:123:10: error: no match for 'operator=' (operand types are 'std::error_code' and 'boost::json::error_code' {aka 'boost::system::error_code'})  
:info:build   123 |     ec = jec;  
:info:build       |          ^~~  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: note: candidate: 'template<class _ErrorCodeEnum> typename std::enable_if<std::is_error_code_enum<_Tp>::value, std::error_code&>::type std::error_code::operator=(_ErrorCodeEnum)'  
:info:build   225 |       operator=(_ErrorCodeEnum __e) noexcept  
:info:build       |       ^~~~~~~~  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: note:   template argument deduction/substitution failed:  
:info:build /opt/local/include/gcc12/c++/system_error: In substitution of 'template<class _ErrorCodeEnum> typename std::enable_if<std::is_error_code_enum<_Tp>::value, std::error_code&>::type std::error_code::operator=(_ErrorCodeEnum) [with _ErrorCodeEnum = boost::system::error_code]':  
:info:build ./boost/json/impl/stream_parser.ipp:123:10:   required from here  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: error: no type named 'type' in 'struct std::enable_if<false, std::error_code&>'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note: candidate: 'std::error_code& std::error_code::operator=(const std::error_code&)'  
:info:build   196 |   class error_code  
:info:build       |         ^~~~~~~~~~  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note:   no known conversion for argument 1 from 'boost::json::error_code' {aka 'boost::system::error_code'} to 'const std::error_code&'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note: candidate: 'std::error_code& std::error_code::operator=(std::error_code&&)'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note:   no known conversion for argument 1 from 'boost::json::error_code' {aka 'boost::system::error_code'} to 'std::error_code&&'  
:info:build ./boost/json/impl/stream_parser.ipp: In member function 'void boost::json::stream_parser::finish(std::error_code&)':  
:info:build ./boost/json/impl/stream_parser.ipp:166:10: error: no match for 'operator=' (operand types are 'std::error_code' and 'boost::json::error_code' {aka 'boost::system::error_code'})  
:info:build   166 |     ec = jec;  
:info:build       |          ^~~  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: note: candidate: 'template<class _ErrorCodeEnum> typename std::enable_if<std::is_error_code_enum<_Tp>::value, std::error_code&>::type std::error_code::operator=(_ErrorCodeEnum)'  
:info:build   225 |       operator=(_ErrorCodeEnum __e) noexcept  
:info:build       |       ^~~~~~~~  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: note:   template argument deduction/substitution failed:  
:info:build /opt/local/include/gcc12/c++/system_error: In substitution of 'template<class _ErrorCodeEnum> typename std::enable_if<std::is_error_code_enum<_Tp>::value, std::error_code&>::type std::error_code::operator=(_ErrorCodeEnum) [with _ErrorCodeEnum = boost::system::error_code]':  
:info:build ./boost/json/impl/stream_parser.ipp:166:10:   required from here  
:info:build /opt/local/include/gcc12/c++/system_error:225:7: error: no type named 'type' in 'struct std::enable_if<false, std::error_code&>'  
:info:build /opt/local/include/gcc12/c++/system_error:196:9: note: candidate: 'std::error_code& std::error_code::operator=(const std::error_code&)'  
:info:build   196 |   class error_code  
:info:build       |         ^~~~~~~~~~  
```

---

## Comment 1

> Username: barracuda156  
> Created at: 2022-10-18 15:19:59 UTC  
> Updated at: 2022-10-18 15:47:12 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1282585500  

Same failure with `gcc11` too.  
  
UPD. Boost 1.79 builds fine on identical setup with `gcc12`. **Only Boost 1.80 fails.**

---

## Comment 2

> Username: grisumbras  
> Created at: 2022-10-18 16:44:06 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1282691878  

We test gcc 11 and 12, although not on Apple. Can you give me the full list of compiler flags being used? `b2 -d+2` prints commands being executed by the build system.

---

## Comment 3

> Username: barracuda156  
> Created at: 2022-10-18 21:07:33 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283004782  

> We test gcc 11 and 12, although not on Apple. Can you give me the full list of compiler flags being used? `b2 -d+2` prints commands being executed by the build system.  
  
@grisumbras Here is the full log.  
[boost180_gcc11.log](https://github.com/boostorg/json/files/9815142/boost180_gcc11.log)

---

## Comment 4

> Username: pdimov  
> Created at: 2022-10-18 22:22:52 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283076038  

One thing to check here is whether an older Boost is in the include path. The above would happen in that case, as the older version of Boost.System won't support the necessary conversion from `boost::system::error_code` to `std::error_code`.

---

## Comment 5

> Username: pdimov  
> Created at: 2022-10-18 22:49:39 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283099226  

MacPorts seems to have 1.76 (in `/opt/local/include/boost`), so if that's installed, it would be new enough for things to mostly work even if the 1.76 headers are picked up occasionally.

---

## Comment 6

> Username: barracuda156  
> Created at: 2022-10-18 23:16:31 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283123638  

> MacPorts seems to have 1.76 (in `/opt/local/include/boost`), so if that's installed, it would be new enough for things to mostly work even if the 1.76 headers are picked up occasionally.  
  
All Boost versions through 1.79 build fine. Only 1.80 errs out.

---

## Comment 7

> Username: barracuda156  
> Created at: 2022-10-18 23:18:06 UTC  
> Updated at: 2022-10-18 23:38:23 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283124835  

> One thing to check here is whether an older Boost is in the include path. The above would happen in that case, as the older version of Boost.System won't support the necessary conversion from `boost::system::error_code` to `std::error_code`.  
  
@pdimov I believe Macports takes care to keep Boost versions separated, but to be on the safe side, I can deactivate all Boost versions and try building 1.80 again.  
  
UPD. No change, so it is not an older header problem.  
[boost180_gcc12.log](https://github.com/boostorg/json/files/9815677/boost180_gcc12.log)

---

## Comment 8

> Username: sdarwin  
> Created at: 2022-10-19 00:47:17 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283193553  

Including old versions of boost headers sounds like a possible explanation.    
  
Searching on a test machine with xcode and brew gcc installed, there weren't any large boost directories. Used the `locate` command. The word "boost" appears, here and there, in the filesystem, but nothing resembling a full installation of boost, unless it was somehow compressed and hidden in another file.

---

## Comment 9

> Username: pdimov  
> Created at: 2022-10-19 01:20:15 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283235910  

My other theory is that for some reason the macro `BOOST_NO_CXX11_HDR_MUTEX` is being defined on this configuration. This leads to Boost.System disabling the `<system_error>` interoperability: https://github.com/boostorg/system/blob/9a6d79b84147854aa919644b56b8553f5a2bedb8/include/boost/system/detail/config.hpp#L22-L24  
  
This can happen when `libstdc++` is compiled as single-threaded, but I have no idea why this would be the case.  
  
Or, by looking at the Config source, it can also happen if timed mutexes aren't available: https://github.com/boostorg/config/blob/2543ff139707a32cc421b62c9d65bb4d9c965b3e/include/boost/config/stdlib/libstdcpp3.hpp#L462-L465

---

## Comment 10

> Username: barracuda156  
> Created at: 2022-10-19 01:23:45 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283244707  

> My other theory is that for some reason the macro `BOOST_NO_CXX11_HDR_MUTEX` is being defined on this configuration.  
  
@pdimov Is this something introduced with Boost 1.80? (No failure with any earlier version, I just rebuilt 1.79, 1.78, 1.76 and 1.71.)

---

## Comment 11

> Username: pdimov  
> Created at: 2022-10-19 01:29:37 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283251728  

It is. Earlier versions implemented conversions to `std::error_code` differently, without needing `std::mutex`, but with a one-time allocation that caused problems with leak checkers. 1.80 uses a different mechanism.

---

## Comment 12

> Username: barracuda156  
> Created at: 2022-10-19 04:44:11 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283419977  

@pdimov Aha, apparently this is exactly what breaks 1.80: https://github.com/boostorg/config/issues/399

---

## Comment 13

> Username: grisumbras  
> Created at: 2022-10-19 08:28:43 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283625733  

The error is related to Boost.System. Apparently, there's a recurring issue on Apple that old version of Boost.System that's already installed on the system is picked up rather than the intended 1.80 version. Can you check that this is not what's happening?

---

## Comment 14

> Username: barracuda156  
> Created at: 2022-10-19 08:41:23 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283642766  

> The error is related to Boost.System. Apparently, there's a recurring issue on Apple that old version of Boost.System that's already installed on the system is picked up rather than the intended 1.80 version. Can you check that this is not what's happening?  
  
@grisumbras I already did – deactivating all Boost versions prior to building 1.80. No change.  
The problem seems to be related to the issue referred above with `libstdc++`.

---

## Comment 15

> Username: grisumbras  
> Created at: 2022-10-19 08:47:49 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283650977  

Oh, my browser haven't pulled the discussion before I posted. Looks like you've figured it out!

---

## Comment 16

> Username: barracuda156  
> Created at: 2022-10-19 08:52:55 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283657767  

> Oh, my browser haven't pulled the discussion before I posted. Looks like you've figured it out!  
  
@grisumbras Yeah, just someone has to fix that now :)

---

## Comment 17

> Username: pdimov  
> Created at: 2022-10-19 09:19:12 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283692535  

Until we figure out how to properly fix this, you should be able to compile Boost 1.80 by defining `BOOST_SYSTEM_HAS_SYSTEM_ERROR` manually by adding `define=BOOST_SYSTEM_HAS_SYSTEM_ERROR` to the `b2` command line.  
  
Users of Boost 1.80 however might have to do that as well, although it should mostly work.

---

## Comment 18

> Username: pdimov  
> Created at: 2022-10-19 09:35:51 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283712989  

Maybe I can use `BOOST_NO_CXX11_HDR_CONDITION_VARIABLE` to check for `<mutex>` instead of the proper macro. It's ridiculous, but looks like it ought to work for this case.

---

## Comment 19

> Username: pdimov  
> Created at: 2022-10-19 10:53:21 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1283811866  

Or maybe you could just patch Boost.Config and comment out https://github.com/boostorg/config/blob/2543ff139707a32cc421b62c9d65bb4d9c965b3e/include/boost/config/stdlib/libstdcpp3.hpp#L464, because it seems to be entirely wrong for GCC 11/12 as those should provide `timed_mutex` on MacOS. :-)

---

## Comment 20

> Username: grisumbras  
> Created at: 2022-11-21 09:29:27 UTC  
> Updated at: 2022-11-21 09:30:02 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1321757212  

@barracuda156 is the problem still present?

---

## Comment 21

> Username: pdimov  
> Created at: 2022-11-21 09:55:35 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1321790727  

Boost.Config has been fixed, so it shouldn't be.

---

## Comment 22

> Username: grisumbras  
> Created at: 2023-07-17 15:48:30 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1638410929  

Closing this as it should be fixed now

---

## Comment 23

> Username: barracuda156  
> Created at: 2023-07-17 16:03:47 UTC  
> Url: https://github.com/boostorg/json/issues/774#issuecomment-1638438410  

> Closing this as it should be fixed now  
  
Boost 1.81 certainly builds fine with GCC.
