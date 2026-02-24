# #108 - UBSAN: downcast of address in interface_oarchive [Closed]

> Username: jeking3  
> Created at: 2018-05-16 17:52:20 UTC  
> Updated at: 2021-01-07 17:43:47 UTC  
> Closed at: 2021-01-07 17:43:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/108  

When building Boost.Serialization with UBSAN enabled I am seeing this error.  I am using the docker build environment in https://github.com/boostorg/boost/pull/184 to make the environment, and then you can see the command below and the issue it finds:  
```  
boost@9423c57fd56c:/boost/libs/serialization/test$ UBSAN_OPTIONS=print_stacktrace=1 ../../../b2 toolset=gcc-7 cxxstd=03 cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined cxxflags=-fno-sanitize-recover=undefined linkflags=-fsanitize=undefined linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold variant=debug -j3 test_bitset_xml_archive  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
...patience...  
...found 2246 targets...  
...updating 2 targets...  
testing.capture-output ../../../bin.v2/libs/serialization/test/test_bitset_xml_archive.test/gcc-7/debug/cxxstd-03-iso/test_bitset_xml_archive.run  
====== BEGIN OUTPUT ======  
../../../boost/archive/detail/interface_oarchive.hpp:47:16: runtime error: downcast of address 0x7ffff8978910 which does not point to an object of type 'xml_oarchive'  
0x7ffff8978910: note: object is of type 'boost::archive::xml_oarchive_impl<boost::archive::xml_oarchive>'  
 c2 fb bf ab  00 38 5e 27 e9 7f 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::archive::xml_oarchive_impl<boost::archive::xml_oarchive>'  
    #0 0x7fe9275a29d0 in boost::archive::detail::interface_oarchive<boost::archive::xml_oarchive>::This() (/boost/bin.v2/libs/serialization/build/gcc-7/debug/cxxstd-03-iso/libboost_serialization.so.1.68.0+0x1859d0)  
    #1 0x7fe92759ead0 in boost::archive::basic_xml_oarchive<boost::archive::xml_oarchive>::init() (/boost/bin.v2/libs/serialization/build/gcc-7/debug/cxxstd-03-iso/libboost_serialization.so.1.68.0+0x181ad0)  
    #2 0x7fe9275a1e76 in boost::archive::xml_oarchive_impl<boost::archive::xml_oarchive>::xml_oarchive_impl(std::ostream&, unsigned int) (/boost/bin.v2/libs/serialization/build/gcc-7/debug/cxxstd-03-iso/libboost_serialization.so.1.68.0+0x184e76)  
    #3 0x55f563efb2f3 in boost::archive::xml_oarchive::xml_oarchive(std::ostream&, unsigned int) ../../../boost/archive/xml_oarchive.hpp:122  
    #4 0x55f563ef8944 in test_main(int, char**) /boost/libs/serialization/test/test_bitset.cpp:47  
    #5 0x55f563ef85be in main /boost/libs/serialization/test/test_tools.hpp:200  
    #6 0x7fe925d86b96 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21b96)  
    #7 0x55f563ef82c9 in _start (/boost/bin.v2/libs/serialization/test/test_bitset_xml_archive.test/gcc-7/debug/cxxstd-03-iso/test_bitset_xml_archive+0xe2c9)  
  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/boost/bin.v2/libs/filesystem/build/gcc-7/debug/cxxstd-03-iso:/boost/bin.v2/libs/serialization/build/gcc-7/debug/cxxstd-03-iso:/boost/bin.v2/libs/system/build/gcc-7/debug/cxxstd-03-iso:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../bin.v2/libs/serialization/test/test_bitset_xml_archive.test/gcc-7/debug/cxxstd-03-iso/test_bitset_xml_archive"   > "../../../bin.v2/libs/serialization/test/test_bitset_xml_archive.test/gcc-7/debug/cxxstd-03-iso/test_bitset_xml_archive.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/serialization/test/test_bitset_xml_archive.test/gcc-7/debug/cxxstd-03-iso/test_bitset_xml_archive.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/serialization/test/test_bitset_xml_archive.test/gcc-7/debug/cxxstd-03-iso/test_bitset_xml_archive.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/serialization/test/test_bitset_xml_archive.test/gcc-7/debug/cxxstd-03-iso/test_bitset_xml_archive.output" "../../../bin.v2/libs/serialization/test/test_bitset_xml_archive.test/gcc-7/debug/cxxstd-03-iso/test_bitset_xml_archive.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/serialization/test/test_bitset_xml_archive.test/gcc-7/debug/cxxstd-03-iso/test_bitset_xml_archive.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../bin.v2/libs/serialization/test/test_bitset_xml_archive.test/gcc-7/debug/cxxstd-03-iso/test_bitset_xml_archive.run...  
...failed updating 1 target...  
...skipped 1 target...  
```

---

## Comment 1

> Username: robertramey  
> Created at: 2018-07-23 01:03:19 UTC  
> Url: https://github.com/boostorg/serialization/issues/108#issuecomment-406911791  

I've looked at this.  
  
It seems that the basic_xml_archive calls init() through the vtable which is not guaranteed to be built yet.  So I'm moving this call from xml_archive_impl to the more derived class xml_archive .  
  
Question: How do you build with UBSAN enabled?  What compiler do you use for this?

---

## Comment 2

> Username: robertramey  
> Created at: 2018-07-23 04:55:19 UTC  
> Url: https://github.com/boostorg/serialization/issues/108#issuecomment-406939009  

ok - reproduced it with Clang.  Unfortunately, a bunch of other stuff from other boost libraries gets flagged as well.  So it's not practical to leave it on all the time.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-07-23 11:40:20 UTC  
> Updated at: 2018-07-23 11:41:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/108#issuecomment-407028536  

I have UBSAN enabled on most of the CMT repository builds at this point and I haven't seen any other issues.  It will be interesting to see if your fixes resolve the failures discovered in the CI PR: https://github.com/boostorg/serialization/pull/87 - once you commit this change I will refresh the PR, or you can submit the change directly into my PR branch and it will rebuild the PR.

---

## Comment 4

> Username: robertramey  
> Created at: 2018-07-23 14:34:00 UTC  
> Url: https://github.com/boostorg/serialization/issues/108#issuecomment-407079392  

Hmmm - I'm the following:  
  
clang-darwin.link ../../../bin.v2/libs/serialization/test/test_simple_class_xml_archive.test/clang-darwin-03/debug/link-static/test_simple_class_xml_archive  
Undefined symbols for architecture x86_64:  
  "___ubsan_handle_add_overflow", referenced from:  
      boost::detail::error_impl(char const*, char const*, int, char const*) in test_simple_class.o  
      boost::detail::test_failed_impl(char const*, char const*, int, char const*) in test_simple_class.o  
      std::__1::basic_filebuf<char, std::__1::char_traits<char> >::sync() in test_simple_class.o  
      std::__1::basic_stringbuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >::seekoff(long long, std::__1::ios_base::seekdir, unsigned int) in test_simple_class.o  
      void boost::archive::basic_text_oprimitive<std::__1::basic_ostream<char, std::__1::char_traits<char> > >::save_impl<float>(float const&, mpl_::bool_<true>&) in test_simple_class.o  
      void boost::archive::basic_text_oprimitive<std::__1::basic_ostream<char, std::__1::char_traits<char> > >::save_impl<double>(double const&, mpl_::bool_<true>&) in test_simple_class.o  
      std::__1::basic_stringbuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >::seekoff(long long, std::__1::ios_base::seekdir, unsigned int) in A.o  
      ...  
  "___ubsan_handle_divrem_overflow", referenced from:  
      std::__1::basic_filebuf<char, std::__1::char_traits<char> >::underflow() in test_simple_class.o  
      std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > boost::math::policies::detail::prec_format<float>(float const&) in test_simple_class.o  
      std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > boost::math::policies::detail::prec_format<double>(double const&) in test_simple_class.o  
      A::() in A.o  
      void randomize<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >&) in A.o  
      void randomize<std::__1::basic_string<wchar_t, std::__1::char_traits<wchar_t>, std::__1::allocator<wchar_t> > >(std::__1::basic_string<wchar_t, std::__1::char_traits<wchar_t>, std::__1::allocator<wchar_t> >&) in A.o  
      std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > boost::math::policies::detail::prec_format<float>(float const&) in A.o  
      ...  
  "___ubsan_handle_dynamic_type_cache_miss", referenced from:  
      boost::system::system_error::what() const in test_simple_class.o  
      boost::system::error_code::message() const in test_simple_class.o  
      _main in test_simple_class.o  
      test_main(int, char**) in test_simple_class.o  
      boost::detail::error_impl(char const*, char const*, int, char const*) in test_simple_class.o  
      boost::report_errors() in test_simple_class.o  
      boost::detail::test_failed_impl(char const*, char const*, int, char const*) in test_simple_class.o  
      ...  
  "___ubsan_handle_float_cast_overflow", referenced from:  
      float boost::math::detail::float_distance_imp<float, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(float const&, float const&, mpl_::bool_<true> const&, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) in test_simple_class.o  
      float boost::math::detail::float_distance_imp<float, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(float const&, float const&, mpl_::bool_<true> const&, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) in A.o  
  "___ubsan_handle_load_invalid_value", referenced from:  
      A::check_equal(A const&) const in test_simple_class.o  
      std::__1::basic_ostream<char, std::__1::char_traits<char> >& std::__1::__put_character_sequence<char, std::__1::char_traits<char> >(std::__1::basic_ostream<char, std::__1::char_traits<char> >&, char const*, unsigned long) in test_simple_class.o  
      boost::detail::report_errors_reminder::~report_errors_reminder() in test_simple_class.o  
      std::__1::basic_filebuf<char, std::__1::char_traits<char> >::~basic_filebuf() in test_simple_class.o  
      std::__1::basic_filebuf<char, std::__1::char_traits<char> >::imbue(std::__1::locale const&) in test_simple_class.o  
      std::__1::basic_filebuf<char, std::__1::char_traits<char> >::setbuf(char*, long) in test_simple_class.o  
      std::__1::basic_filebuf<char, std::__1::char_traits<char> >::seekoff(long long, std::__1::ios_base::seekdir, unsigned int) in test_simple_class.o  
      ...  
  "___ubsan_handle_mul_overflow", referenced from:  
      std::__1::basic_filebuf<char, std::__1::char_traits<char> >::seekoff(long long, std::__1::ios_base::seekdir, unsigned int) in test_simple_class.o  
      std::__1::basic_filebuf<char, std::__1::char_traits<char> >::sync() in test_simple_class.o  
      A::() in A.o  
      boost::filesystem::detail::unique_path(boost::filesystem::path const&, boost::system::error_code*) in libboost_filesystem.a(unique_path.o)  
      boost::filesystem::path_traits::convert(char const*, char const*, std::__1::basic_string<wchar_t, std::__1::char_traits<wchar_t>, std::__1::allocator<wchar_t> >&, std::__1::codecvt<wchar_t, char, __mbstate_t> const&) in libboost_filesystem.a(path_traits.o)  
      boost::filesystem::path_traits::convert(wchar_t const*, wchar_t const*, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >&, std::__1::codecvt<wchar_t, char, __mbstate_t> const&) in libboost_filesystem.a(path_traits.o)  
      boost::filesystem::detail::utf8_codecvt_facet::do_in(__mbstate_t&, char const*, char const*, char const*&, wchar_t*, wchar_t*, wchar_t*&) const in libboost_filesystem.a(utf8_codecvt_facet.o)  
      ...  
  "___ubsan_handle_negate_overflow", referenced from:  
      std::__1::char_traits<char>::eof() in test_simple_class.o  
      std::__1::basic_filebuf<char, std::__1::char_traits<char> >::seekoff(long long, std::__1::ios_base::seekdir, unsigned int) in test_simple_class.o  
      std::__1::basic_filebuf<char, std::__1::char_traits<char> >::seekpos(std::__1::fpos<__mbstate_t>, unsigned int) in test_simple_class.o  
      std::__1::basic_filebuf<char, std::__1::char_traits<char> >::sync() in test_simple_class.o  
      std::__1::basic_filebuf<char, std::__1::char_traits<char> >::pbackfail(int) in test_simple_class.o  
      int boost::math::sign<float>(float const&) in test_simple_class.o  
      std::__1::basic_stringbuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >::seekoff(long long, std::__1::ios_base::seekdir, unsigned int) in test_simple_class.o  
      ...  
  "___ubsan_handle_out_of_bounds", referenced from:  
      boost::archive::tmpnam(char*) in test_simple_class.o  
      std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > boost::math::policies::detail::prec_format<float>(float const&) in test_simple_class.o  
      std::__1::basic_stringbuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >::str() const in test_simple_class.o  
      std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > boost::math::policies::detail::prec_format<double>(double const&) in test_simple_class.o  
      A::() in A.o  
      std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > boost::math::policies::detail::prec_format<float>(float const&) in A.o  
      std::__1::basic_stringbuf<char, std::__1::char_traits<char>, std::__1::allocator<char> >::str() const in A.o  
      ...  
  "___ubsan_handle_shift_out_of_bounds", referenced from:  
      boost::filesystem::detail::unique_path(boost::filesystem::path const&, boost::system::error_code*) in libboost_filesystem.a(unique_path.o)  
      boost::filesystem::detail::utf8_codecvt_facet::do_in(__mbstate_t&, char const*, char const*, char const*&, wchar_t*, wchar_t*, wchar_t*&) const in libboost_filesystem.a(utf8_codecvt_facet.o)  
      boost::filesystem::detail::utf8_codecvt_facet::do_out(__mbstate_t&, wchar_t const*, wchar_t const*, wchar_t const*&, char*, char*, char*&) const in libboost_filesystem.a(utf8_codecvt_facet.o)  
      __isctype(int, unsigned long) in libboost_serialization.a(xml_grammar.o)  
      boost::archive::iterators::transform_width<boost::archive::iterators::binary_from_base64<boost::archive::iterators::remove_whitespace<boost::archive::iterators::istream_iterator<char> >, int>, 8, 6, char>::fill() in libboost_serialization.a(basic_text_iprimitive.o)  
      boost::archive::iterators::transform_width<char const*, 6, 8, char>::fill() in libboost_serialization.a(basic_text_oprimitive.o)  
      boost::archive::detail::utf8_codecvt_facet::do_in(__mbstate_t&, char const*, char const*, char const*&, wchar_t*, wchar_t*, wchar_t*&) const in libboost_serialization.a(utf8_codecvt_facet.o)  
      ...  
  "___ubsan_handle_sub_overflow", referenced from:  
      std::__1::ostreambuf_iterator<char, std::__1::char_traits<char> > std::__1::__pad_and_output<char, std::__1::char_traits<char> >(std::__1::ostreambuf_iterator<char, std::__1::char_traits<char> >, char const*, char const*, char const*, std::__1::ios_base&, char) in test_simple_class.o  
      std::__1::basic_filebuf<char, std::__1::char_traits<char> >::sync() in test_simple_class.o  
      float boost::math::detail::float_distance_imp<float, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(float const&, float const&, mpl_::bool_<true> const&, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) in test_simple_class.o  
      char const* std::__1::__search<bool (*)(char, char), char const*, char const*>(char const*, char const*, char const*, char const*, bool (*)(char, char), std::__1::random_access_iterator_tag, std::__1::random_access_iterator_tag) in test_simple_class.o  
      double boost::math::detail::float_distance_imp<double, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(double const&, double const&, mpl_::bool_<true> const&, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) in test_simple_class.o  
      void randomize<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > >(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >&) in A.o  
      void randomize<std::__1::basic_string<wchar_t, std::__1::char_traits<wchar_t>, std::__1::allocator<wchar_t> > >(std::__1::basic_string<wchar_t, std::__1::char_traits<wchar_t>, std::__1::allocator<wchar_t> >&) in A.o  
      ...  
  "___ubsan_handle_type_mismatch", referenced from:  
      boost::system::system_error::what() const in test_simple_class.o  
      boost::system::error_code::message() const in test_simple_class.o  
      boost::archive::tmpnam(char*) in test_simple_class.o  
      boost::filesystem::path::path(char const*) in test_simple_class.o  
      boost::filesystem::unique_path(boost::filesystem::path const&) in test_simple_class.o  
      boost::filesystem::path::~path() in test_simple_class.o  
      boost::filesystem::operator/(boost::filesystem::path const&, boost::filesystem::path const&) in test_simple_class.o  
      ...  
  "___ubsan_vptr_type_cache", referenced from:  
      boost::system::system_error::what() const in test_simple_class.o  
      boost::system::error_code::message() const in test_simple_class.o  
      _main in test_simple_class.o  
      test_main(int, char**) in test_simple_class.o  
      boost::detail::error_impl(char const*, char const*, int, char const*) in test_simple_class.o  
      boost::report_errors() in test_simple_class.o  
      boost::detail::test_failed_impl(char const*, char const*, int, char const*) in test_simple_class.o  
      ...  
ld: symbol(s) not found for architecture x86_64  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++"   -o "../../../bin.v2/libs/serialization/test/test_simple_class_xml_archive.test/clang-darwin-03/debug/link-static/test_simple_class_xml_archive" "../../../bin.v2/libs/serialization/test/test_simple_class_xml_archive.test/clang-darwin-03/debug/link-static/test_simple_class.o" "../../../bin.v2/libs/serialization/test/test_simple_class_xml_archive.test/clang-darwin-03/debug/link-static/A.o" "../../../bin.v2/libs/serialization/build/clang-darwin-03/debug/link-static/libboost_serialization.a" "../../../bin.v2/libs/filesystem/build/clang-darwin-03/debug/link-static/libboost_filesystem.a" "../../../bin.v2/libs/system/build/clang-darwin-03/debug/link-static/libboost_system.a"        -m64 -g  
  
...failed clang-darwin.link ../../../bin.v2/libs/serialization/test/test_simple_class_xml_archive.test/clang-darwin-03/debug/link-static/test_simple_class_xml_archive...

---

## Comment 5

> Username: jeking3  
> Created at: 2018-07-23 16:15:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/108#issuecomment-407114855  

I did find issues in ptr_container with UBSAN as well, so yes issues exist elsewhere, for example https://github.com/boostorg/ptr_container/issues/18 - in this case I marked the job as "allowed to fail" and opened an issue (like this one) to track its resolution so that I could check in and enable CI.  
  
However system, filesystem, math - they may not be running UBSAN as part of CI yet.  Likely legitimate issues that need to be resolved.

---

## Comment 6

> Username: robertramey  
> Created at: 2018-07-23 17:27:20 UTC  
> Url: https://github.com/boostorg/serialization/issues/108#issuecomment-407137041  

Hmm - upon reflection, I don't think I'm actually reproducing the problem.  I would like to know what compiler one is using and the switches: variant, link etc.

---

## Comment 7

> Username: robertramey  
> Created at: 2018-07-23 17:39:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/108#issuecomment-407140740  

just looking at code seems to indicate a problem though.  I'm invoking init() from xml_oarchive_impl constructor.  Since its the base class of xml_oarchive xml_oarchive isn't fully created yet.  Down casting in the constructor to the lower base class might work - even though it's not kosher.  I can fix this by (maybe) invoking init() from the ctor of xml_oarchive.  But this would create problems for anyone who derives from xml_oarchive_impl.  So I'm in a quandary here for lots of reasons.

---

## Comment 8

> Username: jeking3  
> Created at: 2018-07-23 20:43:01 UTC  
> Updated at: 2018-07-23 20:43:24 UTC  
> Url: https://github.com/boostorg/serialization/issues/108#issuecomment-407194480  

As stated in the original description I am using the docker PR in boostorg/boost to create the environment and then running UBSAN under gcc-7.  The command line I used to run it within the docker container is shown.  
  
It sounds like some refactoring is in order here to resolve the init() issue.  It would be nice to isolate the UBSAN issues in the other components as well.  I can help do that UBSAN investigation for the other repositories and file issues as I find them.

---

## Comment 9

> Username: jeking3  
> Created at: 2018-07-23 20:56:32 UTC  
> Url: https://github.com/boostorg/serialization/issues/108#issuecomment-407198379  

I did the following in the docker container for Boost.System and found no errors:  
  
```  
boost@a7d451ec0735:/boost/libs/system/test$ UBSAN_OPTIONS=print_stacktrace=1 ../../../b2 toolset=gcc-7 cxxstd=03 cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined cxxflags=-fno-sanitize-recover=undefined linkflags=-fsanitize=undefined linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold variant=debug -q  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
...patience...  
...found 986 targets...  
```  
  
I first ran it with -a to make sure it ran everything.

---

## Comment 10

> Username: jeking3  
> Created at: 2018-07-24 10:45:48 UTC  
> Updated at: 2018-07-24 10:49:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/108#issuecomment-407363191  

I found an issue in filesystem https://github.com/boostorg/filesystem/issues/80 as well as one in math https://github.com/boostorg/math/issues/135

---

## Comment 11

> Username: jzmaddock  
> Created at: 2018-07-30 12:13:52 UTC  
> Url: https://github.com/boostorg/serialization/issues/108#issuecomment-408842843  

Robert - the errors you posted above are linker errors, not sanitizer errors.  
You can reproduce with:  
```  
UBSAN_OPTIONS=print_stacktrace=1 ../../../b2 toolset=clang cxxstd=03 cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined cxxflags=-fno-sanitize-recover=undefined linkflags=-fsanitize=undefined linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold variant=debug test_simple_class_xml_archive  
```  
Note the linkflags.  
  
For me, this then produces the sanitizer error:  
  
```  
testing.capture-output ../../../bin.v2/libs/serialization/test/test_simple_class_xml_archive.test/clang-linux-6.0.0/debug/cxxstd-03-iso/test_simple_class_xml_archive.run  
====== BEGIN OUTPUT ======  
A.cpp:85:19: runtime error: signed integer overflow: 1804289383 * 846930886 cannot be represented in type 'int'  
    #0 0x46b82c  (/home/jzm/boost/boost/bin.v2/libs/serialization/test/test_simple_class_xml_archive.test/clang-linux-6.0.0/debug/cxxstd-03-iso/test_simple_class_xml_archive+0x46b82c)  
    #1 0x432117  (/home/jzm/boost/boost/bin.v2/libs/serialization/test/test_simple_class_xml_archive.test/clang-linux-6.0.0/debug/cxxstd-03-iso/test_simple_class_xml_archive+0x432117)  
    #2 0x431ee5  (/home/jzm/boost/boost/bin.v2/libs/serialization/test/test_simple_class_xml_archive.test/clang-linux-6.0.0/debug/cxxstd-03-iso/test_simple_class_xml_archive+0x431ee5)  
    #3 0x7f44624cab96  (/lib/x86_64-linux-gnu/libc.so.6+0x21b96)  
    #4 0x40a4b9  (/home/jzm/boost/boost/bin.v2/libs/serialization/test/test_simple_class_xml_archive.test/clang-linux-6.0.0/debug/cxxstd-03-iso/test_simple_class_xml_archive+0x40a4b9)  
```  
  
Which indicates an error in the test case rather than the library.  
  
However, there are issues with using the library as well, as for example the multiprecision serialization tests fail with for example:  
  
```  
../../../boost/archive/detail/interface_oarchive.hpp:47:16: runtime error: downcast of address 0x7ffcb09c3ed8 which does not point to an object of type 'boost::archive::text_oarchive'  
0x7ffcb09c3ed8: note: object is of type 'boost::archive::text_oarchive_impl<boost::archive::text_oarchive>'  
 da 7f 00 00  88 b5 e9 53 da 7f 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::archive::text_oarchive_impl<boost::archive::text_oarchive>'  
    #0 0x7fda53dc9bc3  (/home/jzm/boost/boost/bin.v2/libs/serialization/build/clang-linux-6.0.0/release/cxxstd-03-iso/libboost_serialization.so.1.68.0+0x82bc3)  
    #1 0x7fda53dcaad5  (/home/jzm/boost/boost/bin.v2/libs/serialization/build/clang-linux-6.0.0/release/cxxstd-03-iso/libboost_serialization.so.1.68.0+0x83ad5)  
    #2 0x432a5d  (/home/jzm/boost/boost/bin.v2/libs/multiprecision/test/test_cpp_int_serial_1.test/clang-linux-6.0.0/release/cxxstd-03-iso/test_cpp_int_serial_1+0x432a5d)  
    #3 0x432950  (/home/jzm/boost/boost/bin.v2/libs/multiprecision/test/test_cpp_int_serial_1.test/clang-linux-6.0.0/release/cxxstd-03-iso/test_cpp_int_serial_1+0x432950)  
    #4 0x7fda5296bb96  (/lib/x86_64-linux-gnu/libc.so.6+0x21b96)  
    #5 0x40b1e9  (/home/jzm/boost/boost/bin.v2/libs/multiprecision/test/test_cpp_int_serial_1.test/clang-linux-6.0.0/release/cxxstd-03-iso/test_cpp_int_serial_1+0x40b1e9)  
  
  
EXIT STATUS: 1  
```

---

## Comment 12

> Username: robertramey  
> Created at: 2018-07-30 15:18:38 UTC  
> Url: https://github.com/boostorg/serialization/issues/108#issuecomment-408900924  

On 7/30/18 5:13 AM, jzmaddock wrote:  
>  
> Robert - the errors you posted above are linker errors, not sanitizer   
> errors.  
> You can reproduce with:  
>  
> |UBSAN_OPTIONS=print_stacktrace=1 ../../../b2 toolset=clang cxxstd=03   
> cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined   
> cxxflags=-fno-sanitize-recover=undefined   
> linkflags=-fsanitize=undefined   
> linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold   
> variant=debug test_simple_class_xml_archive |  
>  
> Note the linkflags.  
>  
> For me, this then produces the sanitizer error:  
>  
> |testing.capture-output   
> ../../../bin.v2/libs/serialization/test/test_simple_class_xml_archive.test/clang-linux-6.0.0/debug/cxxstd-03-iso/test_simple_class_xml_archive.run   
> ====== BEGIN OUTPUT ====== A.cpp:85:19: runtime error: signed integer   
> overflow: 1804289383 * 846930886 cannot be represented in type 'int'   
> #0 0x46b82c   
> (/home/jzm/boost/boost/bin.v2/libs/serialization/test/test_simple_class_xml_archive.test/clang-linux-6.0.0/debug/cxxstd-03-iso/test_simple_class_xml_archive+0x46b82c)   
> #1 0x432117   
> (/home/jzm/boost/boost/bin.v2/libs/serialization/test/test_simple_class_xml_archive.test/clang-linux-6.0.0/debug/cxxstd-03-iso/test_simple_class_xml_archive+0x432117)   
> #2 0x431ee5   
> (/home/jzm/boost/boost/bin.v2/libs/serialization/test/test_simple_class_xml_archive.test/clang-linux-6.0.0/debug/cxxstd-03-iso/test_simple_class_xml_archive+0x431ee5)   
> #3 0x7f44624cab96 (/lib/x86_64-linux-gnu/libc.so.6+0x21b96) #4   
> 0x40a4b9   
> (/home/jzm/boost/boost/bin.v2/libs/serialization/test/test_simple_class_xml_archive.test/clang-linux-6.0.0/debug/cxxstd-03-iso/test_simple_class_xml_archive+0x40a4b9)   
> |  
>  
> Which indicates an error in the test case rather than the library.  
>  
This looks relatively easy to fix.  I'll look into it.  
>  
> However, there are issues with using the library as well, as for   
> example the multiprecision serialization tests fail with for example:  
>  
> |../../../boost/archive/detail/interface_oarchive.hpp:47:16: runtime   
> error: downcast of address 0x7ffcb09c3ed8 which does not point to an   
> object of type 'boost::archive::text_oarchive' 0x7ffcb09c3ed8: note:   
> object is of type   
> 'boost::archive::text_oarchive_impl<boost::archive::text_oarchive>' da   
> 7f 00 00 88 b5 e9 53 da 7f 00 00 00 00 00 00 00 00 00 00 00 00 00 00   
> 00 00 00 00 00 00 00 00 ^~~~~~~~~~~~~~~~~~~~~~~ vptr for   
> 'boost::archive::text_oarchive_impl<boost::archive::text_oarchive>' #0   
> 0x7fda53dc9bc3   
> (/home/jzm/boost/boost/bin.v2/libs/serialization/build/clang-linux-6.0.0/release/cxxstd-03-iso/libboost_serialization.so.1.68.0+0x82bc3)   
> #1 0x7fda53dcaad5   
> (/home/jzm/boost/boost/bin.v2/libs/serialization/build/clang-linux-6.0.0/release/cxxstd-03-iso/libboost_serialization.so.1.68.0+0x83ad5)   
> #2 0x432a5d   
> (/home/jzm/boost/boost/bin.v2/libs/multiprecision/test/test_cpp_int_serial_1.test/clang-linux-6.0.0/release/cxxstd-03-iso/test_cpp_int_serial_1+0x432a5d)   
> #3 0x432950   
> (/home/jzm/boost/boost/bin.v2/libs/multiprecision/test/test_cpp_int_serial_1.test/clang-linux-6.0.0/release/cxxstd-03-iso/test_cpp_int_serial_1+0x432950)   
> #4 0x7fda5296bb96 (/lib/x86_64-linux-gnu/libc.so.6+0x21b96) #5   
> 0x40b1e9   
> (/home/jzm/boost/boost/bin.v2/libs/multiprecision/test/test_cpp_int_serial_1.test/clang-linux-6.0.0/release/cxxstd-03-iso/test_cpp_int_serial_1+0x40b1e9)   
> EXIT STATUS: 1|  
|I did look closely at this one. "Fixing" it would require changing the   
API at the user level for those making their own archives. True - it is   
undefined behavior.  But it doesn't seem to create any unexpected   
consequences in any environment.  So I'm not inclined to change the code   
to eliminate this issue. My preferred manner  to address this by adding   
some comments in the code and perhaps a note in the documentation.    
Ideally I'd like to overide this error in this specific spot so the that   
UBSAN emits a warning.|  
> ||  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/serialization/issues/108#issuecomment-408842843>,   
> or mute the thread   
> <https://github.com/notifications/unsubscribe-auth/AB4R3NCp3C3S4TZuq4PuM4IDmt7fcBsmks5uLviAgaJpZM4UBwsW>.  
>  
  
--   
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 13

> Username: o01eg  
> Created at: 2021-01-07 12:56:40 UTC  
> Url: https://github.com/boostorg/serialization/issues/108#issuecomment-756100181  

Looks like it possible to catch this UB without sanitizers https://github.com/godotengine/godot/issues/44987

---

## Comment 14

> Username: robertramey  
> Created at: 2021-01-07 17:43:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/108#issuecomment-756270573  

it looks like an ABI change triggers this issue.  That is why the problem is resolved when building godot from source.  it seems to see that this would be the definitive way to address this issue.
