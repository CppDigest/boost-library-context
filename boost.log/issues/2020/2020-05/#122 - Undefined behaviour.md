# #122 - Undefined behaviour? [Closed]

> Username: matthijs  
> Created at: 2020-05-10 19:41:55 UTC  
> Updated at: 2020-05-13 07:06:07 UTC  
> Closed at: 2020-05-10 19:57:01 UTC  
> Url: https://github.com/boostorg/log/issues/122  

Hi,  
  
The following test program causes undefined behaviour:  
```c++  
#include <boost/log/sources/global_logger_storage.hpp>  
#include <boost/log/sources/severity_logger.hpp>  
#include <boost/log/sources/record_ostream.hpp>  
#include <boost/log/utility/setup/console.hpp>  
  
// Severity level  
enum class severity_t  
{  
  info  
};  
  
std::ostream& operator<<(std::ostream& os, const severity_t& level)  
{  
  switch (level)  
  {  
    case severity_t::info:  
      os << "info";  
      break;  
  }  
  return os;  
}  
  
BOOST_LOG_INLINE_GLOBAL_LOGGER_DEFAULT(mm_logger, boost::log::sources::severity_logger<severity_t>);  
  
int main(int argc, char** argv)  
{  
  boost::log::add_console_log(std::clog, boost::log::keywords::format = "[TimeStamp]: (Severity) %Message%");  
  
  BOOST_LOG_STREAM_WITH_PARAMS(mm_logger::get(), (::boost::log::keywords::severity = severity_t::info)) << "test log";  
  
  return 0U;  
}  
```  
The compile line I used:  
clang++ -o main -DBOOST_ALL_DYN_LINK -DBOOST_BEAST_USE_STD_STRING_VIEW -I/boost/boost_1_73_0 -std=c++17 -stdlib=libc++ -fno-omit-frame-pointer -O1 -fsanitize=address,undefined,leak -fsanitize-address-use-after-scope -L/boost/boost_1_73_0/stage/lib -lboost_log -lboost_log_setup -lboost_filesystem -lboost_system -lboost_regex -lboost_date_time -lboost_atomic -lboost_chrono -lboost_thread -g main.cpp  
  
Compiler version: clang 7  
Boost version: 1.73.0 (using libc++ as standard library, also compiled with clang 7)  
  
It produces the following output:  
```c++  
/boost/boost_1_73_0/boost/log/utility/formatting_ostream.hpp:737:29: runtime error: member call on address 0x6120000001c8 which does not point to an object of type 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
0x6120000001c8: note: object is of type 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
 00 00 00 00  a8 c5 bd ff a3 7f 00 00  e0 25 95 ff a3 7f 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /boost/boost_1_73_0/boost/log/utility/formatting_ostream.hpp:737:29 in  
/boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:176:14: runtime error: member access within address 0x6120000001c8 which does not point to an object of type 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
0x6120000001c8: note: object is of type 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
 00 00 00 00  a8 c5 bd ff a3 7f 00 00  e0 25 95 ff a3 7f 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:176:14 in  
/boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:178:13: runtime error: member access within address 0x6120000001c8 which does not point to an object of type 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
0x6120000001c8: note: object is of type 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
 00 00 00 00  a8 c5 bd ff a3 7f 00 00  e0 25 95 ff a3 7f 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:178:13 in  
/boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:180:30: runtime error: member call on address 0x6120000001c8 which does not point to an object of type 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
0x6120000001c8: note: object is of type 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
 00 00 00 00  a8 c5 bd ff a3 7f 00 00  e0 25 95 ff a3 7f 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:180:30 in  
/boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:167:9: runtime error: member access within address 0x6120000001c8 which does not point to an object of type 'const boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
0x6120000001c8: note: object is of type 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
 00 00 00 00  a8 c5 bd ff a3 7f 00 00  e0 25 95 ff a3 7f 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:167:9 in  
/boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:169:32: runtime error: member access within address 0x6120000001c8 which does not point to an object of type 'const boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
0x6120000001c8: note: object is of type 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
 00 00 00 00  a8 c5 bd ff a3 7f 00 00  e0 25 95 ff a3 7f 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:169:32 in  
/boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:170:23: runtime error: member access within address 0x6120000001c8 which does not point to an object of type 'const boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
0x6120000001c8: note: object is of type 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
 00 00 00 00  a8 c5 bd ff a3 7f 00 00  e0 25 95 ff a3 7f 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:170:23 in  
/boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:170:50: runtime error: member access within address 0x6120000001c8 which does not point to an object of type 'const boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
0x6120000001c8: note: object is of type 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
 00 00 00 00  a8 c5 bd ff a3 7f 00 00  e0 25 95 ff a3 7f 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:170:50 in  
/boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:184:17: runtime error: member access within address 0x6120000001c8 which does not point to an object of type 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
0x6120000001c8: note: object is of type 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
 00 00 00 00  a8 c5 bd ff a3 7f 00 00  e0 25 95 ff a3 7f 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::log::v2_mt_posix::aux::basic_ostringstreambuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /boost/boost_1_73_0/boost/log/detail/attachable_sstream_buf.hpp:184:17 in  
[TimeStamp]: (Severity) test log  
  
=================================================================  
==11714==ERROR: LeakSanitizer: detected memory leaks  
  
Direct leak of 18432 byte(s) in 18 object(s) allocated from:  
    #0 0x4ca763 in malloc (/t1/main+0x4ca763)  
    #1 0x7fa3ff866a54 in __cxa_demangle (/usr/lib/x86_64-linux-gnu/libc++abi.so.1+0xfa54)  
  
SUMMARY: AddressSanitizer: 18432 byte(s) leaked in 18 allocation(s).  
```  
  
Regards, Matthijs

---

## Comment 1

> Username: matthijs  
> Created at: 2020-05-10 19:51:29 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626380241  

On slack some people pointed out that it has to do with a missing BOOST_SYMBOL_VISIBLE for the mentioned class. Hopefully this will make it easier to resolve this.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-05-10 19:57:01 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626380929  

This seems like a bogus error, as the error mentions the same type, as in "it's not type X, it's type X". Please, report it to clang or try a newer version, in case if it's fixed already.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-05-10 19:59:14 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626381192  

If clang relies on default visibility for type checking, you may need to add `-fvisibility=default` to your compiler options.

---

## Comment 4

> Username: matthijs  
> Created at: 2020-05-10 20:04:39 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626381810  

I've added that to my compile line, but still the same output.

---

## Comment 5

> Username: Lastique  
> Created at: 2020-05-10 20:31:24 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626385274  

If that error is triggered from within Boost binaries, you may also need to rebuild Boost with `visibility=global` in `b2` command line.

---

## Comment 6

> Username: pdimov  
> Created at: 2020-05-10 21:46:18 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626394492  

This is not considered a bug by the sanitizer developers. Even though the two types have the same name, they are still different (when compiled with hidden visibility). To make them the same, you have to use BOOST_SYMBOL_VISIBLE on the class.

---

## Comment 7

> Username: Lastique  
> Created at: 2020-05-10 23:03:55 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626403367  

This is not a bug in the library either as `BOOST_SYMBOL_VISIBLE` is not needed for it to function. Marking it for the sake of a testing tool is not acceptable.  
  
AFAIR, gcc developers have switched to comparing type infos by values at some point (i.e. they compare type name strings now). That is specifically to support hidden visibility. So, it seems the correct way to compare types these days is to compare by value, which means this is a bug in the sanitizer.

---

## Comment 8

> Username: pdimov  
> Created at: 2020-05-10 23:11:09 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626404146  

Debatable. There is no guarantee that two types with distinct typeinfos are the same even if they happen to share the same name. Either way, this doesn't help our users, since Boost is now built with hidden visibility by default.

---

## Comment 9

> Username: Lastique  
> Created at: 2020-05-10 23:20:16 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626405239  

Two types having the same name are the same type. This is guaranteed by name mangling. If they are, in fact, different types then you have an ODR violation.  
  
We chose hidden visibility by default for a reason. I'm not going to make symbols visible again for compatibility with testing tools.

---

## Comment 10

> Username: pdimov  
> Created at: 2020-05-10 23:23:13 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626405630  

Do whatever, as long as you realize that it makes the library unusable with -fsanitize=undefined.

---

## Comment 11

> Username: Lastique  
> Created at: 2020-05-10 23:25:07 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626405868  

I'm fine with that. You can always build Boost with `visibility=global` if the sanitizers require it.

---

## Comment 12

> Username: pdimov  
> Created at: 2020-05-10 23:27:52 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626406203  

Actually, I checked later versions of Clang, and this is no longer a problem in 9 or later. So they must have fixed it.

---

## Comment 13

> Username: Lastique  
> Created at: 2020-05-10 23:28:22 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626406268  

Good to know, thanks.

---

## Comment 14

> Username: Flamefire  
> Created at: 2020-05-11 09:08:28 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626575066  

> Two types having the same name are the same type. This is guaranteed by name mangling. If they are, in fact, different types then you have an ODR violation.  
  
AFAIK this is not true for virtual classes. If you have a virtual class with hidden visibility than the VTABLE will be put into each object and NOT be merged by the dynamic linker. This also includes the typeinfo. So the visibility on a class affects the VTABLE/typeinfo     
While this usually does not cause a problem besides slightly degraded performance and increased binary size it IS a problem for exceptions. When throwing from one object and catching in another than it may fail as the VTABLEs/typeinfos don't match.  
  
This is actually an intended effect of hidden visibility: That you can have 2 private types with the same name and not clash them when an unrelated, third-party library has the same name for a type.  
  
> they compare type name strings now  
  
I'm curious: Do you have a source? Because this contradicts what I mentioned above which I'm very sure of.  
  
Anyway: Be cautious for exception types and the mentioned change might mean that behavior can differ on different compiler versions. And the problematic scenario (2 different shared libs with private linkage each both linking shared boost and passing types between boundaries) isn't exactly well tested.

---

## Comment 15

> Username: Lastique  
> Created at: 2020-05-11 09:58:39 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626602022  

> AFAIK this is not true for virtual classes.  
  
What is a virtual class?  
  
> When throwing from one object and catching in another than it may fail as the VTABLEs/typeinfos don't match.  
  
On older compilers, type info must have been globally visible for exceptions and `dynamic_cast` to work. This is because the compiler runtime used to compare pointers to the typeinfo structures rather than comparing their values. This is why we mark exceptions and types intended to be used with `dynamic_cast` with `BOOST_SYMBOL_VISIBLE`. Newer compilers compare typeinfos by value, which means `BOOST_SYMBOL_VISIBLE` is technically not required, but we still keep it for backward compatibility and because it may improve performance (i.e. the comparison may be faster if two typeinfos have the same address). Given this, I don't see what is broken.  
  
> I'm curious: Do you have a source?  
  
https://github.com/gcc-mirror/gcc/blob/dbeaa7ab81a37acadc9af6e7990332604252de20/libstdc%2B%2B-v3/libsupc%2B%2B/typeinfo#L102

---

## Comment 16

> Username: Flamefire  
> Created at: 2020-05-11 10:09:38 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626607973  

> What is a virtual class?  
  
Classes with virtual members i.e. having a VTABLE.  
  
> mark exceptions and types intended to be used with dynamic_cast with BOOST_SYMBOL_VISIBLE  
  
That is a useful rule of thumb and matches my understanding.  
  
> Given this, I don't see what is broken.  
  
If that `boost::log::v2_mt_posix::aux::basic_ostringstreambuf` is not used as above (dynamic_cast) which I don't think it is, then all is fine.

---

## Comment 17

> Username: Lastique  
> Created at: 2020-05-11 10:30:56 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626618992  

> Two types having the same name are the same type. This is guaranteed by name mangling. If they are, in fact, different types then you have an ODR violation.  
  
> AFAIK this is not true for virtual classes.  
  
VTABLE is irrelevant to my quoted statement or to typeinfos comparing equal or not. It is not what is being compared. It is only used to obtain the typeinfo in run time, which is then used to compare the types. When you mark a type with `BOOST_SYMBOL_VISIBLE`, the compiler conveniently marks 3 symbols globally visible: vtable, typeinfo and typeinfo name, which makes this whole thing work.  
  
And if you have two actually different types under the same (fully qualified) name, you have an ODR violation regardless of how the compiler does typeinfo comparison, or whether the typeinfo is globally visible or not. Expect obscure problems in this case either way.

---

## Comment 18

> Username: Lastique  
> Created at: 2020-05-11 10:34:14 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626620616  

> the compiler conveniently marks 3 symbols globally visible: vtable, typeinfo and typeinfo name  
  
Actually, it's 4 - I forgot vtordisp (virtual base class displacement table), if one is present.

---

## Comment 19

> Username: Flamefire  
> Created at: 2020-05-11 10:47:07 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626626630  

> VTABLE is irrelevant to my quoted statement  
  
"Virtual classes" are likely/could be used by `dynamic_cast`. So what I meant: For those classes (having a vtable and the 3 others) with hidden visibility using them in a `dynamic_cast` will make them look like separate classes even though they have the same fully qualified name. IIRC the dynamic_cast boils (or boiled? has it changed?) down to comparing the vtable pointers (with some magic to cater for hierarchies)  
  
  
> if you have two actually different types under the same (fully qualified) name, you have an ODR violation  
  
FWIW I just looked up the part for symbol clashes solved by hidden visibility I mentioned earlier:  
  
> Much lower chance of symbol collision. The old woe of two libraries internally using the same symbol for different things is finally behind us with this patch. Hallelujah!   
  
from https://gcc.gnu.org/wiki/Visibility. This sounds very much like it is possible with hidden visibility to use the same fully qualified name if those are only used internally (no compilation unit sees both of them)

---

## Comment 20

> Username: Lastique  
> Created at: 2020-05-11 10:57:26 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626631391  

> "Virtual classes" are likely/could be used by dynamic_cast.  
  
They *could* be used, but definitely not *likely*. Virtual functions are used much much more often than `dynamic_cast`.  
  
> IIRC the dynamic_cast boils (or boiled? has it changed?) down to comparing the vtable pointers (with some magic to cater for hierarchies)  
  
To my knowledge, `dynamic_cast` simply compares typeinfos for equality while traversing the list of base classes.  
  
> from https://gcc.gnu.org/wiki/Visibility.  
  
I think, that article was written before compilers started comparing typeinfos by value. Obviously, if your two types have the same name, they will compare equal, so the clash can happen in a context where typeinfos are compared. But it is still *much* lower chance of clash than before, when you had *every* symbol in the program globally visible.

---

## Comment 21

> Username: Flamefire  
> Created at: 2020-05-11 11:02:21 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-626633659  

> To my knowledge, dynamic_cast simply compares typeinfos for equality while traversing the list of base classes.  
  
If you want to cater for the "same name = same type" issue then this makes sense to. Anyway for non-recent compilers that don't do this the problem is the same.  
  
> I think, that article was written before compilers started comparing typeinfos by value.  
  
Makes sense. I guess it means that it is fine until you do anything with type_info, vtable etc.

---

## Comment 22

> Username: matthijs  
> Created at: 2020-05-13 07:06:07 UTC  
> Url: https://github.com/boostorg/log/issues/122#issuecomment-627791455  

I've upgraded to llvm 11 and I can confirm that the error disappeared.
