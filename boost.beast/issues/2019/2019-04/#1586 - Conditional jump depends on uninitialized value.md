# #1586 - Conditional jump depends on uninitialized value [Closed]

> Username: djarek  
> Created at: 2019-04-22 21:14:51 UTC  
> Updated at: 2019-05-04 16:04:53 UTC  
> Closed at: 2019-05-04 16:04:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1586  

A C++14 build (debug) with gcc8 (Ubuntu 19.04) yields valgrind (valgrind-3.14.0) errors when run:  
```  
damian@V3560:~/projects/boost$ valgrind bin.v2/libs/beast/test/beast/zlib/gcc-8/debug/cxxstd-14-iso/link-static/target-os-linux/threadapi-pthread/threading-multi/visibility-hidden/fat-tests  
==8828== Memcheck, a memory error detector  
==8828== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.  
==8828== Using Valgrind-3.14.0 and LibVEX; rerun with -h for copyright info  
==8828== Command: bin.v2/libs/beast/test/beast/zlib/gcc-8/debug/cxxstd-14-iso/link-static/target-os-linux/threadapi-pthread/threading-multi/visibility-hidden/fat-tests  
==8828==   
beast.zlib.deflate_stream  
sizeof(deflate_stream) == 5936  
==8828== Conditional jump or move depends on uninitialised value(s)  
==8828==    at 0x14AA4D: boost::beast::zlib::detail::deflate_stream::fill_window(boost::beast::zlib::z_params&) (deflate_stream.ipp:1546)  
==8828==    by 0x14B54A: boost::beast::zlib::detail::deflate_stream::f_stored(boost::beast::zlib::z_params&, boost::beast::zlib::Flush) (deflate_stream.ipp:1840)  
==8828==    by 0x15C460: boost::beast::zlib::detail::deflate_stream::deflate_stored(boost::beast::zlib::z_params&, boost::beast::zlib::Flush) (deflate_stream.hpp:670)  
==8828==    by 0x148387: boost::beast::zlib::detail::deflate_stream::doWrite(boost::beast::zlib::z_params&, boost::optional<boost::beast::zlib::Flush>, boost::system::error_code&) (deflate_stream.ipp:435)  
==8828==    by 0x1326A6: boost::beast::zlib::deflate_stream::write(boost::beast::zlib::z_params&, boost::beast::zlib::Flush, boost::system::error_code&) (deflate_stream.hpp:289)  
==8828==    by 0x132DBB: boost::beast::zlib::deflate_stream_test::doDeflate1_beast(int, int, int, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (deflate_stream.cpp:192)  
==8828==    by 0x1335F9: boost::beast::zlib::deflate_stream_test::doMatrix(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, void (boost::beast::zlib::deflate_stream_test::*)(int, int, int, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&)) (deflate_stream.cpp:289)  
==8828==    by 0x13378F: boost::beast::zlib::deflate_stream_test::testDeflate() (deflate_stream.cpp:302)  
==8828==    by 0x1338A4: boost::beast::zlib::deflate_stream_test::run() (deflate_stream.cpp:312)  
==8828==    by 0x12F38D: void boost::beast::unit_test::suite::run<void>(boost::beast::unit_test::runner&) (suite.hpp:595)  
==8828==    by 0x12E7B2: void boost::beast::unit_test::suite::operator()<void>(boost::beast::unit_test::runner&) (suite.hpp:434)  
==8828==    by 0x1346B7: boost::beast::unit_test::suite_info boost::beast::unit_test::make_suite_info<boost::beast::zlib::deflate_stream_test>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, bool)::{lambda(boost::beast::unit_test::runner&)#1}::operator()(boost::beast::unit_test::runner&) const (suite_info.hpp:117)  
==8828==   
==8828== Conditional jump or move depends on uninitialised value(s)  
==8828==    at 0x14AA4D: boost::beast::zlib::detail::deflate_stream::fill_window(boost::beast::zlib::z_params&) (deflate_stream.ipp:1546)  
==8828==    by 0x14C55C: boost::beast::zlib::detail::deflate_stream::f_huff(boost::beast::zlib::z_params&, boost::beast::zlib::Flush) (deflate_stream.ipp:2249)  
==8828==    by 0x15C510: boost::beast::zlib::detail::deflate_stream::deflate_huff(boost::beast::zlib::z_params&, boost::beast::zlib::Flush) (deflate_stream.hpp:694)  
==8828==    by 0x1482E3: boost::beast::zlib::detail::deflate_stream::doWrite(boost::beast::zlib::z_params&, boost::optional<boost::beast::zlib::Flush>, boost::system::error_code&) (deflate_stream.ipp:428)  
==8828==    by 0x1326A6: boost::beast::zlib::deflate_stream::write(boost::beast::zlib::z_params&, boost::beast::zlib::Flush, boost::system::error_code&) (deflate_stream.hpp:289)  
==8828==    by 0x132DBB: boost::beast::zlib::deflate_stream_test::doDeflate1_beast(int, int, int, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (deflate_stream.cpp:192)  
==8828==    by 0x1335F9: boost::beast::zlib::deflate_stream_test::doMatrix(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, void (boost::beast::zlib::deflate_stream_test::*)(int, int, int, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&)) (deflate_stream.cpp:289)  
==8828==    by 0x13378F: boost::beast::zlib::deflate_stream_test::testDeflate() (deflate_stream.cpp:302)  
==8828==    by 0x1338A4: boost::beast::zlib::deflate_stream_test::run() (deflate_stream.cpp:312)  
==8828==    by 0x12F38D: void boost::beast::unit_test::suite::run<void>(boost::beast::unit_test::runner&) (suite.hpp:595)  
==8828==    by 0x12E7B2: void boost::beast::unit_test::suite::operator()<void>(boost::beast::unit_test::runner&) (suite.hpp:434)  
==8828==    by 0x1346B7: boost::beast::unit_test::suite_info boost::beast::unit_test::make_suite_info<boost::beast::zlib::deflate_stream_test>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, bool)::{lambda(boost::beast::unit_test::runner&)#1}::operator()(boost::beast::unit_test::runner&) const (suite_info.hpp:117)  
==8828==   
==8828== Conditional jump or move depends on uninitialised value(s)  
==8828==    at 0x14AA4D: boost::beast::zlib::detail::deflate_stream::fill_window(boost::beast::zlib::z_params&) (deflate_stream.ipp:1546)  
==8828==    by 0x14C109: boost::beast::zlib::detail::deflate_stream::f_rle(boost::beast::zlib::z_params&, boost::beast::zlib::Flush) (deflate_stream.ipp:2167)  
==8828==    by 0x15C4E4: boost::beast::zlib::detail::deflate_stream::deflate_rle(boost::beast::zlib::z_params&, boost::beast::zlib::Flush) (deflate_stream.hpp:688)  
==8828==    by 0x14830C: boost::beast::zlib::detail::deflate_stream::doWrite(boost::beast::zlib::z_params&, boost::optional<boost::beast::zlib::Flush>, boost::system::error_code&) (deflate_stream.ipp:431)  
==8828==    by 0x1326A6: boost::beast::zlib::deflate_stream::write(boost::beast::zlib::z_params&, boost::beast::zlib::Flush, boost::system::error_code&) (deflate_stream.hpp:289)  
==8828==    by 0x132DBB: boost::beast::zlib::deflate_stream_test::doDeflate1_beast(int, int, int, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (deflate_stream.cpp:192)  
==8828==    by 0x1335F9: boost::beast::zlib::deflate_stream_test::doMatrix(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, void (boost::beast::zlib::deflate_stream_test::*)(int, int, int, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&)) (deflate_stream.cpp:289)  
==8828==    by 0x13378F: boost::beast::zlib::deflate_stream_test::testDeflate() (deflate_stream.cpp:302)  
==8828==    by 0x1338A4: boost::beast::zlib::deflate_stream_test::run() (deflate_stream.cpp:312)  
==8828==    by 0x12F38D: void boost::beast::unit_test::suite::run<void>(boost::beast::unit_test::runner&) (suite.hpp:595)  
==8828==    by 0x12E7B2: void boost::beast::unit_test::suite::operator()<void>(boost::beast::unit_test::runner&) (suite.hpp:434)  
==8828==    by 0x1346B7: boost::beast::unit_test::suite_info boost::beast::unit_test::make_suite_info<boost::beast::zlib::deflate_stream_test>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, bool)::{lambda(boost::beast::unit_test::runner&)#1}::operator()(boost::beast::unit_test::runner&) const (suite_info.hpp:117)  
==8828==   
==8828== Conditional jump or move depends on uninitialised value(s)  
==8828==    at 0x14AA4D: boost::beast::zlib::detail::deflate_stream::fill_window(boost::beast::zlib::z_params&) (deflate_stream.ipp:1546)  
==8828==    by 0x14B7AC: boost::beast::zlib::detail::deflate_stream::f_fast(boost::beast::zlib::z_params&, boost::beast::zlib::Flush) (deflate_stream.ipp:1910)  
==8828==    by 0x15C48C: boost::beast::zlib::detail::deflate_stream::deflate_fast(boost::beast::zlib::z_params&, boost::beast::zlib::Flush) (deflate_stream.hpp:676)  
==8828==    by 0x148387: boost::beast::zlib::detail::deflate_stream::doWrite(boost::beast::zlib::z_params&, boost::optional<boost::beast::zlib::Flush>, boost::system::error_code&) (deflate_stream.ipp:435)  
==8828==    by 0x1326A6: boost::beast::zlib::deflate_stream::write(boost::beast::zlib::z_params&, boost::beast::zlib::Flush, boost::system::error_code&) (deflate_stream.hpp:289)  
==8828==    by 0x132DBB: boost::beast::zlib::deflate_stream_test::doDeflate1_beast(int, int, int, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) (deflate_stream.cpp:192)  
==8828==    by 0x1335F9: boost::beast::zlib::deflate_stream_test::doMatrix(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, void (boost::beast::zlib::deflate_stream_test::*)(int, int, int, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&)) (deflate_stream.cpp:289)  
==8828==    by 0x13378F: boost::beast::zlib::deflate_stream_test::testDeflate() (deflate_stream.cpp:302)  
==8828==    by 0x1338A4: boost::beast::zlib::deflate_stream_test::run() (deflate_stream.cpp:312)  
==8828==    by 0x12F38D: void boost::beast::unit_test::suite::run<void>(boost::beast::unit_test::runner&) (suite.hpp:595)  
==8828==    by 0x12E7B2: void boost::beast::unit_test::suite::operator()<void>(boost::beast::unit_test::runner&) (suite.hpp:434)  
==8828==    by 0x1346B7: boost::beast::unit_test::suite_info boost::beast::unit_test::make_suite_info<boost::beast::zlib::deflate_stream_test>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, bool)::{lambda(boost::beast::unit_test::runner&)#1}::operator()(boost::beast::unit_test::runner&) const (suite_info.hpp:117)  
==8828==   
beast.zlib.error  
beast.zlib.inflate_stream  
sizeof(inflate_stream) == 7104  
==8828== Conditional jump or move depends on uninitialised value(s)  
==8828==    at 0x118A55: slide_hash (deflate.c:219)  
==8828==    by 0x11BA7B: fill_window (deflate.c:1516)  
==8828==    by 0x11C678: deflate_fast (deflate.c:1838)  
==8828==    by 0x11AC44: deflate (deflate.c:1003)  
==8828==    by 0x13568C: boost::beast::zlib::inflate_stream_test::compress[abi:cxx11](boost::basic_string_view<char, std::char_traits<char> > const&, int, int, int, int) (inflate_stream.cpp:96)  
==8828==    by 0x13656D: void boost::beast::zlib::inflate_stream_test::Matrix::operator()<boost::beast::zlib::inflate_stream_test::Beast>(boost::beast::zlib::inflate_stream_test::Beast const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) const (inflate_stream.cpp:275)  
==8828==    by 0x135FB3: boost::beast::zlib::inflate_stream_test::testInflate() (inflate_stream.cpp:305)  
==8828==    by 0x136448: boost::beast::zlib::inflate_stream_test::run() (inflate_stream.cpp:371)  
==8828==    by 0x12F38D: void boost::beast::unit_test::suite::run<void>(boost::beast::unit_test::runner&) (suite.hpp:595)  
==8828==    by 0x12E7B2: void boost::beast::unit_test::suite::operator()<void>(boost::beast::unit_test::runner&) (suite.hpp:434)  
==8828==    by 0x136E39: boost::beast::unit_test::suite_info boost::beast::unit_test::make_suite_info<boost::beast::zlib::inflate_stream_test>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, bool)::{lambda(boost::beast::unit_test::runner&)#1}::operator()(boost::beast::unit_test::runner&) const (suite_info.hpp:117)  
==8828==    by 0x13715E: std::_Function_handler<void (boost::beast::unit_test::runner&), boost::beast::unit_test::suite_info boost::beast::unit_test::make_suite_info<boost::beast::zlib::inflate_stream_test>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, bool)::{lambda(boost::beast::unit_test::runner&)#1}>::_M_invoke(std::_Any_data const&, boost::beast::unit_test::runner&) (std_function.h:297)  
==8828==   
==8828== Conditional jump or move depends on uninitialised value(s)  
==8828==    at 0x118A55: slide_hash (deflate.c:219)  
==8828==    by 0x11BA7B: fill_window (deflate.c:1516)  
==8828==    by 0x11DFD7: deflate_huff (deflate.c:2139)  
==8828==    by 0x11ABF4: deflate (deflate.c:1001)  
==8828==    by 0x13568C: boost::beast::zlib::inflate_stream_test::compress[abi:cxx11](boost::basic_string_view<char, std::char_traits<char> > const&, int, int, int, int) (inflate_stream.cpp:96)  
==8828==    by 0x13656D: void boost::beast::zlib::inflate_stream_test::Matrix::operator()<boost::beast::zlib::inflate_stream_test::Beast>(boost::beast::zlib::inflate_stream_test::Beast const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) const (inflate_stream.cpp:275)  
==8828==    by 0x135FB3: boost::beast::zlib::inflate_stream_test::testInflate() (inflate_stream.cpp:305)  
==8828==    by 0x136448: boost::beast::zlib::inflate_stream_test::run() (inflate_stream.cpp:371)  
==8828==    by 0x12F38D: void boost::beast::unit_test::suite::run<void>(boost::beast::unit_test::runner&) (suite.hpp:595)  
==8828==    by 0x12E7B2: void boost::beast::unit_test::suite::operator()<void>(boost::beast::unit_test::runner&) (suite.hpp:434)  
==8828==    by 0x136E39: boost::beast::unit_test::suite_info boost::beast::unit_test::make_suite_info<boost::beast::zlib::inflate_stream_test>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, bool)::{lambda(boost::beast::unit_test::runner&)#1}::operator()(boost::beast::unit_test::runner&) const (suite_info.hpp:117)  
==8828==    by 0x13715E: std::_Function_handler<void (boost::beast::unit_test::runner&), boost::beast::unit_test::suite_info boost::beast::unit_test::make_suite_info<boost::beast::zlib::inflate_stream_test>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, bool)::{lambda(boost::beast::unit_test::runner&)#1}>::_M_invoke(std::_Any_data const&, boost::beast::unit_test::runner&) (std_function.h:297)  
==8828==   
==8828== Conditional jump or move depends on uninitialised value(s)  
==8828==    at 0x118A55: slide_hash (deflate.c:219)  
==8828==    by 0x11BA7B: fill_window (deflate.c:1516)  
==8828==    by 0x11D90D: deflate_rle (deflate.c:2071)  
==8828==    by 0x11AC16: deflate (deflate.c:1002)  
==8828==    by 0x13568C: boost::beast::zlib::inflate_stream_test::compress[abi:cxx11](boost::basic_string_view<char, std::char_traits<char> > const&, int, int, int, int) (inflate_stream.cpp:96)  
==8828==    by 0x13656D: void boost::beast::zlib::inflate_stream_test::Matrix::operator()<boost::beast::zlib::inflate_stream_test::Beast>(boost::beast::zlib::inflate_stream_test::Beast const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) const (inflate_stream.cpp:275)  
==8828==    by 0x135FB3: boost::beast::zlib::inflate_stream_test::testInflate() (inflate_stream.cpp:305)  
==8828==    by 0x136448: boost::beast::zlib::inflate_stream_test::run() (inflate_stream.cpp:371)  
==8828==    by 0x12F38D: void boost::beast::unit_test::suite::run<void>(boost::beast::unit_test::runner&) (suite.hpp:595)  
==8828==    by 0x12E7B2: void boost::beast::unit_test::suite::operator()<void>(boost::beast::unit_test::runner&) (suite.hpp:434)  
==8828==    by 0x136E39: boost::beast::unit_test::suite_info boost::beast::unit_test::make_suite_info<boost::beast::zlib::inflate_stream_test>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, bool)::{lambda(boost::beast::unit_test::runner&)#1}::operator()(boost::beast::unit_test::runner&) const (suite_info.hpp:117)  
==8828==    by 0x13715E: std::_Function_handler<void (boost::beast::unit_test::runner&), boost::beast::unit_test::suite_info boost::beast::unit_test::make_suite_info<boost::beast::zlib::inflate_stream_test>(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, bool)::{lambda(boost::beast::unit_test::runner&)#1}>::_M_invoke(std::_Any_data const&, boost::beast::unit_test::runner&) (std_function.h:297)  
==8828==   
369.0s, 3 suites, 3 cases, 2154617 tests total, 0 failures  
==8828==   
==8828== HEAP SUMMARY:  
==8828==     in use at exit: 0 bytes in 0 blocks  
==8828==   total heap usage: 3,841,159 allocs, 3,841,159 frees, 36,138,533,117 bytes allocated  
==8828==   
==8828== All heap blocks were freed -- no leaks are possible  
==8828==   
==8828== For counts of detected and suppressed errors, rerun with: -v  
==8828== Use --track-origins=yes to see where uninitialised values come from  
==8828== ERROR SUMMARY: 225411 errors from 7 contexts (suppressed: 0 from 0)  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-22 22:44:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1586#issuecomment-485577912  

Well is it a real bug?

---

## Comment 2

> Username: djarek  
> Created at: 2019-04-22 22:48:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1586#issuecomment-485578982  

This function is listed in the valgrind suppression file: https://github.com/boostorg/beast/blob/develop/tools/valgrind.supp#L9  
However, the mangled name changed in this commit, 2 months ago:  
https://github.com/boostorg/beast/commit/955354b9dd5da254fe43d344695b149be1aa742b  
  
Even if this is not a bug, then there's an issue with CI, because the valgrind check should've been failing.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-04-23 01:54:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1586#issuecomment-485612562  

I don't remember adding that but I guess I was frustrated because I couldn't figure out why it was happening.
