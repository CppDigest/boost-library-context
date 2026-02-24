# #111 - UBSAN: does not point to an object of type 'system_error' @ gregorian/testdate_input_facet.cpp:338 [Closed]

> Username: jeking3  
> Created at: 2019-10-14 20:38:28 UTC  
> Updated at: 2022-04-25 02:21:52 UTC  
> Closed at: 2022-04-25 02:21:52 UTC  
> Url: https://github.com/boostorg/date_time/issues/111  

https://travis-ci.org/boostorg/date_time/jobs/597602723#L1287  
```  
/usr/include/c++/8/bits/ios_base.h:233:12: runtime error: member access within address 0x7ffce2926a20 which does not point to an object of type 'system_error'  
0x7ffce2926a20: note: object is of type 'std::ios_base::failure[abi:cxx11]'  
 fc 7f 00 00  48 7f 9c c0 d2 7f 00 00  a8 72 31 02 00 00 00 00  01 00 00 00 00 00 00 00  d0 00 9d c0  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'std::ios_base::failure[abi:cxx11]'  
    #0 0x478f13 in std::ios_base::system_error::system_error(std::ios_base::system_error const&) (/home/travis/build/boostorg/boost-root/bin.v2/libs/date_time/test/testdate_input_facet.test/gcc-8/debug/cxxstd-03-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/testdate_input_facet+0x478f13)  
    #1 0x47935e in std::ios_base::failure[abi:cxx11]::failure(std::ios_base::failure[abi:cxx11] const&) (/home/travis/build/boostorg/boost-root/bin.v2/libs/date_time/test/testdate_input_facet.test/gcc-8/debug/cxxstd-03-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/testdate_input_facet+0x47935e)  
    #2 0x49cde0 in boost::exception_detail::error_info_injector<std::ios_base::failure[abi:cxx11]>::error_info_injector(std::ios_base::failure[abi:cxx11] const&) (/home/travis/build/boostorg/boost-root/bin.v2/libs/date_time/test/testdate_input_facet.test/gcc-8/debug/cxxstd-03-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/testdate_input_facet+0x49cde0)  
    #3 0x4985de in boost::exception_detail::enable_error_info_return_type<std::ios_base::failure[abi:cxx11]>::type boost::enable_error_info<std::ios_base::failure[abi:cxx11]>(std::ios_base::failure[abi:cxx11] const&) (/home/travis/build/boostorg/boost-root/bin.v2/libs/date_time/test/testdate_input_facet.test/gcc-8/debug/cxxstd-03-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/testdate_input_facet+0x4985de)  
    #4 0x48be10 in boost::wrapexcept<boost::exception_detail::remove_error_info_injector<std::ios_base::failure[abi:cxx11]>::type> boost::exception_detail::enable_both<std::ios_base::failure[abi:cxx11]>(std::ios_base::failure[abi:cxx11] const&) (/home/travis/build/boostorg/boost-root/bin.v2/libs/date_time/test/testdate_input_facet.test/gcc-8/debug/cxxstd-03-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/testdate_input_facet+0x48be10)  
    #5 0x479fc1 in void boost::throw_exception<std::ios_base::failure[abi:cxx11]>(std::ios_base::failure[abi:cxx11] const&) (/home/travis/build/boostorg/boost-root/bin.v2/libs/date_time/test/testdate_input_facet.test/gcc-8/debug/cxxstd-03-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/testdate_input_facet+0x479fc1)  
    #6 0x490902 in boost::date_time::date_generator_parser<boost::gregorian::date, char>::extract_element(std::istreambuf_iterator<char, std::char_traits<char> >&, std::istreambuf_iterator<char, std::char_traits<char> >&, boost::date_time::date_generator_parser<boost::gregorian::date, char>::phrase_elements) const (/home/travis/build/boostorg/boost-root/bin.v2/libs/date_time/test/testdate_input_facet.test/gcc-8/debug/cxxstd-03-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/testdate_input_facet+0x490902)  
    #7 0x481392 in boost::date_time::first_kday_before<boost::gregorian::date> boost::date_time::date_generator_parser<boost::gregorian::date, char>::get_kday_before_type<boost::date_time::date_input_facet<boost::gregorian::date, char, std::istreambuf_iterator<char, std::char_traits<char> > > >(std::istreambuf_iterator<char, std::char_traits<char> >&, std::istreambuf_iterator<char, std::char_traits<char> >&, std::ios_base&, boost::date_time::date_input_facet<boost::gregorian::date, char, std::istreambuf_iterator<char, std::char_traits<char> > > const&) const (/home/travis/build/boostorg/boost-root/bin.v2/libs/date_time/test/testdate_input_facet.test/gcc-8/debug/cxxstd-03-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/testdate_input_facet+0x481392)  
    #8 0x4703f3 in boost::date_time::date_input_facet<boost::gregorian::date, char, std::istreambuf_iterator<char, std::char_traits<char> > >::get(std::istreambuf_iterator<char, std::char_traits<char> >&, std::istreambuf_iterator<char, std::char_traits<char> >&, std::ios_base&, boost::date_time::first_kday_before<boost::gregorian::date>&) const (/home/travis/build/boostorg/boost-root/bin.v2/libs/date_time/test/testdate_input_facet.test/gcc-8/debug/cxxstd-03-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/testdate_input_facet+0x4703f3)  
    #9 0x4509b6 in std::basic_istream<char, std::char_traits<char> >& boost::gregorian::operator>><char, std::char_traits<char> >(std::basic_istream<char, std::char_traits<char> >&, boost::date_time::first_kday_before<boost::gregorian::date>&) (/home/travis/build/boostorg/boost-root/bin.v2/libs/date_time/test/testdate_input_facet.test/gcc-8/debug/cxxstd-03-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/testdate_input_facet+0x4509b6)  
    #10 0x454f04 in bool failure_test<boost::date_time::first_kday_before<boost::gregorian::date>, std::ios_base::failure[abi:cxx11]>(boost::date_time::first_kday_before<boost::gregorian::date>, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, std::ios_base::failure[abi:cxx11] const&, boost::date_time::date_input_facet<boost::gregorian::date, char, std::istreambuf_iterator<char, std::char_traits<char> > >*) (/home/travis/build/boostorg/boost-root/bin.v2/libs/date_time/test/testdate_input_facet.test/gcc-8/debug/cxxstd-03-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/testdate_input_facet+0x454f04)  
    #11 0x412a16 in main ../../libs/date_time/test/gregorian/testdate_input_facet.cpp:338  
    #12 0x7fd2bf4c982f in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x2082f)  
    #13 0x40adb8 in _start (/home/travis/build/boostorg/boost-root/bin.v2/libs/date_time/test/testdate_input_facet.test/gcc-8/debug/cxxstd-03-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/testdate_input_facet+0x40adb8)  
EXIT STATUS: 1  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2022-02-11 13:15:48 UTC  
> Updated at: 2022-02-11 14:28:38 UTC  
> Url: https://github.com/boostorg/date_time/issues/111#issuecomment-1036203215  

This is still reproducible, in fact it causes 60+ failures when you run the date_time tests with ubsan.  
  
https://github.com/boostorg/date_time/runs/5148137918?check_suite_focus=true#step:12:3389  
  
```  
/usr/include/c++/11/bits/ios_base.h:233:12: runtime error: member access within address 0x7ffd5e513620 which does not point to an object of type 'system_error'  
0x7ffd5e513620: note: object is of type 'std::ios_base::failure[abi:cxx11]'  
 60 61 00 00  c8 dd 7b 92 94 7f 00 00  b8 5d 11 00 80 60 00 00  01 00 00 00 00 00 00 00  d0 61 7c 92  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'std::ios_base::failure[abi:cxx11]'  
    #0 0x56234ff1de02 in std::ios_base::system_error::system_error(std::ios_base::system_error const&) /usr/include/c++/11/bits/ios_base.h:233  
    #1 0x56234ff1e358 in std::ios_base::failure[abi:cxx11]::failure(std::ios_base::failure[abi:cxx11] const&) /usr/include/c++/11/bits/ios_base.h:255  
    #2 0x56234ff379f0 in boost::wrapexcept<std::ios_base::failure[abi:cxx11]>::wrapexcept(std::ios_base::failure[abi:cxx11] const&) boost/throw_exception.hpp:95  
    #3 0x56234ff1ee2f in void boost::throw_exception<std::ios_base::failure[abi:cxx11]>(std::ios_base::failure[abi:cxx11] const&) boost/throw_exception.hpp:162  
    #4 0x56234ff3e06f in boost::date_time::date_generator_parser<boost::gregorian::date, char>::extract_element(std::istreambuf_iterator<char, std::char_traits<char> >&, std::istreambuf_iterator<char, std::char_traits<char> >&, boost::date_time::date_generator_parser<boost::gregorian::date, char>::phrase_elements) const boost/date_time/date_generator_parser.hpp:284  
    #5 0x56234ff29c78 in boost::date_time::first_kday_before<boost::gregorian::date> boost::date_time::date_generator_parser<boost::gregorian::date, char>::get_kday_before_type<boost::date_time::date_input_facet<boost::gregorian::date, char, std::istreambuf_iterator<char, std::char_traits<char> > > >(std::istreambuf_iterator<char, std::char_traits<char> >&, std::istreambuf_iterator<char, std::char_traits<char> >&, std::ios_base&, boost::date_time::date_input_facet<boost::gregorian::date, char, std::istreambuf_iterator<char, std::char_traits<char> > > const&) const boost/date_time/date_generator_parser.hpp:248  
    #6 0x56234ff0f841 in boost::date_time::date_input_facet<boost::gregorian::date, char, std::istreambuf_iterator<char, std::char_traits<char> > >::get(std::istreambuf_iterator<char, std::char_traits<char> >&, std::istreambuf_iterator<char, std::char_traits<char> >&, std::ios_base&, boost::date_time::first_kday_before<boost::gregorian::date>&) const boost/date_time/date_facet.hpp:688  
    #7 0x56234fee4e61 in std::basic_istream<char, std::char_traits<char> >& boost::gregorian::operator>><char, std::char_traits<char> >(std::basic_istream<char, std::char_traits<char> >&, boost::date_time::first_kday_before<boost::gregorian::date>&) boost/date_time/gregorian/gregorian_io.hpp:756  
    #8 0x56234feeabde in bool failure_test<boost::date_time::first_kday_before<boost::gregorian::date>, std::ios_base::failure[abi:cxx11]>(boost::date_time::first_kday_before<boost::gregorian::date>, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, std::ios_base::failure[abi:cxx11] const&, boost::date_time::date_input_facet<boost::gregorian::date, char, std::istreambuf_iterator<char, std::char_traits<char> > >*) libs/date_time/test/gregorian/testdate_input_facet.cpp:30  
    #9 0x56234fe88925 in main libs/date_time/test/gregorian/testdate_input_facet.cpp:338  
    #10 0x7f94918e40b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)  
    #11 0x56234fe7980d in _start (/home/runner/work/date_time/boost-root/bin.v2/libs/date_time/test/testdate_input_facet.test/gcc-11/debug/address-sanitizer-norecover/cxxstd-03-iso/threading-multi/undefined-sanitizer-norecover/visibility-hidden/testdate_input_facet+0x13180d)  
```  
  
For this particular case the calling code from date_time is:  
```  
boost::throw_exception(std::ios_base::failure("Parse failed. No match found for '" + mr.cache + "'"));  
```

---

## Comment 2

> Username: jeking3  
> Created at: 2022-02-13 11:50:07 UTC  
> Url: https://github.com/boostorg/date_time/issues/111#issuecomment-1038049513  

std::ios_base::failure in particular is a special case (see last paragraph, first section):  
  
https://gcc.gnu.org/onlinedocs/libstdc++/manual/using_dual_abi.html  
  
If I build and ubsan the test with cxxstd=03 define="_GLIBCXX_USE_CXX11_ABI=0" then I do not see the issue but leaving the ABI at c++11, I do see it.
