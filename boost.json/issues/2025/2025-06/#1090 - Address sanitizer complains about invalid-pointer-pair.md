# #1090 - Address sanitizer complains about invalid-pointer-pair [Open]

> Username: cmorve-te  
> Created at: 2025-06-30 21:10:06 UTC  
> Updated at: 2025-07-03 08:04:09 UTC  
> Url: https://github.com/boostorg/json/issues/1090  

This simple program  
```  
#include <boost/json/src.hpp>  
  
int main() {  
  const auto payload = boost::json::serialize(boost::json::object{{"field", "value"}});  
}  
```  
, built with `g++ -g -o main main.cpp -I boost_1_88_0 -fsanitize=address -fsanitize=pointer-subtract`, and run as `ASAN_OPTIONS="detect_invalid_pointer_pairs=1" ./main`.  
  
Results in  
```  
$ ASAN_OPTIONS="detect_invalid_pointer_pairs=1" ./test   
=================================================================  
==20812==ERROR: AddressSanitizer: invalid-pointer-pair: 0x7998dc601040 0x7998dc600042  
    #0 0x000000435ac5 in boost::json::detail::stream::remain() const boost_1_88_0/boost/json/detail/stream.hpp:265  
    #1 0x000000449789 in bool boost::json::detail::do_write_string<true>(boost::json::detail::writer&, boost::json::detail::stream&) boost_1_88_0/boost/json/impl/serializer.ipp:228  
    #2 0x000000414960 in boost::json::detail::write_string(boost::json::detail::writer&, boost::json::detail::stream&) boost_1_88_0/boost/json/impl/serializer.ipp:348  
    #3 0x00000044c86c in bool boost::json::detail::write_impl<boost::json::object, true>(boost::json::detail::map_like_conversion_tag, boost::json::detail::writer&, boost::json::detail::stream&) boost_1_88_0/boost/json/impl/serializer.hpp:336  
    #4 0x00000044c86c in bool boost::json::detail::write_object<true>(boost::json::detail::writer&, boost::json::detail::stream&) boost_1_88_0/boost/json/impl/serializer.ipp:380  
    #5 0x000000415678 in boost::json::serializer::read(char*, unsigned long) boost_1_88_0/boost/json/impl/serializer.ipp:548  
    #6 0x000000447e03 in boost::core::basic_string_view<char> boost::json::serializer::read<4096ul>(char (&) [4096ul]) boost_1_88_0/boost/json/serializer.hpp:292  
    #7 0x000000412b67 in boost::json::detail::serialize_impl(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, boost::json::serializer&) boost_1_88_0/boost/json/impl/serialize.ipp:64  
    #8 0x000000413417 in boost::json::serialize[abi:cxx11](boost::json::object const&, boost::json::serialize_options const&) boost_1_88_0/boost/json/impl/serialize.ipp:147  
    #9 0x0000004272be in main /test/test.cpp:5  
    #10 0x7d98de2ae5f4 in __libc_start_call_main (/lib64/libc.so.6+0x35f4) (BuildId: 7504db94dbf054e06eaac49950f57161c601f5c6)  
    #11 0x7d98de2ae6a7 in __libc_start_main@@GLIBC_2.34 (/lib64/libc.so.6+0x36a7) (BuildId: 7504db94dbf054e06eaac49950f57161c601f5c6)  
    #12 0x0000004009b4 in _start (/test/test+0x4009b4) (BuildId: c19e3884d2a136258e826e7c13e90e801896f155)  
  
Address 0x7998dc601040 is located in stack of thread T0 at offset 4160 in frame  
    #0 0x000000412a22 in boost::json::detail::serialize_impl(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, boost::json::serializer&) boost_1_88_0/boost/json/impl/serialize.ipp:59  
  
  This frame has 2 object(s):  
    [32, 48) 'sv' (line 63)  
    [64, 4160) 'buf' (line 62) <== Memory access at offset 4160 overflows this variable  
HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork  
      (longjmp and C++ exceptions *are* supported)  
Address 0x7998dc600042 is located in stack of thread T0 at offset 66 in frame  
    #0 0x000000412a22 in boost::json::detail::serialize_impl(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, boost::json::serializer&) boost_1_88_0/boost/json/impl/serialize.ipp:59  
  
  This frame has 2 object(s):  
    [32, 48) 'sv' (line 63)  
    [64, 4160) 'buf' (line 62) <== Memory access at offset 66 is inside this variable  
HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork  
      (longjmp and C++ exceptions *are* supported)  
SUMMARY: AddressSanitizer: invalid-pointer-pair boost_1_88_0/boost/json/detail/stream.hpp:265 in boost::json::detail::stream::remain() const  
==20812==ABORTING  
```  
  
This was done in a Fedora 42 system, with gcc 15.1 (the Fedora patched version).  
  
It seems related to https://github.com/boostorg/json/pull/663?

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-07-01 06:38:13 UTC  
> Url: https://github.com/boostorg/json/issues/1090#issuecomment-3022073666  

So, I have spent a bunch of time on this back in the day, and my conclusion was that ASAN has a bug where it considers a pointer into a buffer and a pointer to one after the buffer to not be comparable if the buffer ends with the memory page.  
Or, in a visual form:  
  
```  
|<-------------------- page 1 ------------------->|<-------------------- page 2 ------------------->|  
        |<------------ buffer ------------------->|0  
                                                   ^  
                                                   one past buffer  
```  
  
Why do I think that? If I reduce `BOOST_JSON_STACK_BUFFER_SIZE` (which controls the sizes of buffers allocated for IO operations) to 2048, I get no ASAN failures. So, try that too, build with  
```  
g++ -g -o main main.cpp -I boost_1_88_0 -fsanitize=address -fsanitize=pointer-subtract -DBOOST_JSON_STACK_BUFFER_SIZE=2048  
```

---

## Comment 2

> Username: cmorve-te  
> Created at: 2025-07-01 10:23:45 UTC  
> Url: https://github.com/boostorg/json/issues/1090#issuecomment-3023233777  

I certainly don't see the issue. I have [reported it to gcc](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=120915). Let's see what they say.

---

## Comment 3

> Username: cmorve-te  
> Created at: 2025-07-03 08:04:09 UTC  
> Url: https://github.com/boostorg/json/issues/1090#issuecomment-3031288864  

Not saying Boost.JSON should do it. But FWIW you can have `__attribute__ ((no_sanitize ("pointer-subtract")))` in `remain()`, and `__attribute__ ((no_sanitize ("pointer-compare")))` in `operator bool()`, and gcc stops complaining. Unfortunately, it's different for clang, you need `__attribute__ ((no_sanitize ("address")))` in both functions instead.
