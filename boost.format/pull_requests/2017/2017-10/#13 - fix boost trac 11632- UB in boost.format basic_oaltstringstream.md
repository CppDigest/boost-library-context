# #13 fix boost trac 11632: UB in boost.format basic_oaltstringstream [Merged]

> Username: jeking3  
> Created at: 2017-10-07 04:15:57 UTC  
> Updated at: 2017-10-18 19:22:25 UTC  
> Merged at: 2017-10-07 10:39:39 UTC  
> Closed at: 2017-10-07 10:39:39 UTC  
> Url: https://github.com/boostorg/format/pull/13  

Trac 11632: UB in boost.format basic_oaltstringstream  
Reproducible in the Ubuntu Zesty or Artful build environments.  
Using boostorg/format, develop branch, build like so:  
```  
$ UBSAN_OPTIONS=print_stacktrace=1 ../../../b2 -q -j2 cxxflags="-fsanitize=undefined -fno-sanitize-recover=undefined" linkflags="-lubsan"  
  
...  
testing.capture-output ../../../bin.v2/libs/format/test/format_test1.test/gcc-6.3.0/debug/format_test1.run  
====== BEGIN OUTPUT ======  
../../../boost/format/alt_sstream.hpp:146:60: runtime error: member call on address 0x7ffc2321f260 which does not point to an object of type 'basic_oaltstringstream'  
0x7ffc2321f260: note: object has invalid vptr  
 fc 7f 00 00  00 00 00 00 00 00 00 00  e8 f6 21 23 fc 7f 00 00  d0 94 af d5 73 55 00 00  28 6b 36 62  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              invalid vptr  
    #0 0x5573d4b5bd87 in boost::io::basic_oaltstringstream<char, std::char_traits<char>, std::allocator<char> >::basic_oaltstringstream(boost::io::basic_altstringbuf<char, std::char_traits<char>, std::allocator<char> >*) (/home/jking/boost/bin.v2/libs/format/test/format_test1.test/gcc-6.3.0/debug/format_test1+0x52d87)  
    #1 0x5573d4b580c2 in void boost::io::detail::put<char, std::char_traits<char>, std::allocator<char>, boost::io::detail::put_holder<char, std::char_traits<char> > const&>(boost::io::detail::put_holder<char, std::char_traits<char> > const&, boost::io::detail::format_item<char, std::char_traits<char>, std::allocator<char> > const&, boost::basic_format<char, std::char_traits<char>, std::allocator<char> >::string_type&, boost::basic_format<char, std::char_traits<char>, std::allocator<char> >::internal_streambuf_t&, std::locale*) (/home/jking/boost/bin.v2/libs/format/test/format_test1.test/gcc-6.3.0/debug/format_test1+0x4f0c2)  
    #2 0x5573d4b5430a in void boost::io::detail::distribute<char, std::char_traits<char>, std::allocator<char>, boost::io::detail::put_holder<char, std::char_traits<char> > const&>(boost::basic_format<char, std::char_traits<char>, std::allocator<char> >&, boost::io::detail::put_holder<char, std::char_traits<char> > const&) (/home/jking/boost/bin.v2/libs/format/test/format_test1.test/gcc-6.3.0/debug/format_test1+0x4b30a)  
    #3 0x5573d4b51474 in boost::basic_format<char, std::char_traits<char>, std::allocator<char> >& boost::io::detail::feed_impl<char, std::char_traits<char>, std::allocator<char>, boost::io::detail::put_holder<char, std::char_traits<char> > const&>(boost::basic_format<char, std::char_traits<char>, std::allocator<char> >&, boost::io::detail::put_holder<char, std::char_traits<char> > const&) (/home/jking/boost/bin.v2/libs/format/test/format_test1.test/gcc-6.3.0/debug/format_test1+0x48474)  
    #4 0x5573d4b4b0de in boost::basic_format<char, std::char_traits<char>, std::allocator<char> >& boost::io::detail::feed<char, std::char_traits<char>, std::allocator<char>, char const (&) [11]>(boost::basic_format<char, std::char_traits<char>, std::allocator<char> >&, char const (&) [11]) (/home/jking/boost/bin.v2/libs/format/test/format_test1.test/gcc-6.3.0/debug/format_test1+0x420de)  
    #5 0x5573d4b47647 in boost::basic_format<char, std::char_traits<char>, std::allocator<char> >& boost::basic_format<char, std::char_traits<char>, std::allocator<char> >::operator%<char [11]>(char const (&) [11]) (/home/jking/boost/bin.v2/libs/format/test/format_test1.test/gcc-6.3.0/debug/format_test1+0x3e647)  
    #6 0x5573d4b3e068 in test_main(int, char**) /home/jking/boost/libs/format/test/format_test1.cpp:40  
    #7 0x5573d4bf075f in test_main_caller::operator()() ../../../boost/test/impl/test_main.ipp:34  
    #8 0x5573d4bf0ade in boost::detail::function::void_function_obj_invoker0<test_main_caller, void>::invoke(boost::detail::function::function_buffer&) ../../../boost/function/function_template.hpp:159  
    #9 0x5573d4c024d6 in boost::function0<void>::operator()() const ../../../boost/function/function_template.hpp:760  
    #10 0x5573d4c01b3e in boost::detail::forward::operator()() ../../../boost/test/impl/execution_monitor.ipp:1300  
    #11 0x5573d4c028e1 in boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) ../../../boost/function/function_template.hpp:138  
    #12 0x5573d4c026bc in boost::function0<int>::operator()() const ../../../boost/function/function_template.hpp:760  
    #13 0x5573d4c01e76 in int boost::detail::do_invoke<boost::shared_ptr<boost::detail::translator_holder_base>, boost::function<int ()> >(boost::shared_ptr<boost::detail::translator_holder_base> const&, boost::function<int ()> const&) ../../../boost/test/impl/execution_monitor.ipp:281  
    #14 0x5573d4c006cc in boost::execution_monitor::catch_signals(boost::function<int ()> const&) ../../../boost/test/impl/execution_monitor.ipp:864  
    #15 0x5573d4c0084f in boost::execution_monitor::execute(boost::function<int ()> const&) ../../../boost/test/impl/execution_monitor.ipp:1203  
    #16 0x5573d4c014d9 in boost::execution_monitor::vexecute(boost::function<void ()> const&) ../../../boost/test/impl/execution_monitor.ipp:1309  
    #17 0x5573d4bf6003 in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned int) ../../../boost/test/impl/unit_test_monitor.ipp:46  
    #18 0x5573d4bd4a05 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) ../../../boost/test/impl/framework.ipp:771  
    #19 0x5573d4bd4430 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) ../../../boost/test/impl/framework.ipp:717  
    #20 0x5573d4bce936 in boost::unit_test::framework::run(unsigned long, bool) ../../../boost/test/impl/framework.ipp:1577  
    #21 0x5573d4b7c666 in boost::unit_test::unit_test_main(boost::unit_test::test_suite* (*)(int, char**), int, char**) ../../../boost/test/impl/unit_test_main.ipp:231  
    #22 0x5573d4b7c925 in main ../../../boost/test/impl/unit_test_main.ipp:286  
    #23 0x7f7d617213f0 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x203f0)  
    #24 0x5573d4b3c6b9 in _start (/home/jking/boost/bin.v2/libs/format/test/format_test1.test/gcc-6.3.0/debug/format_test1+0x336b9)  
```  
With this code patch:  
```  
$ UBSAN_OPTIONS=print_stacktrace=1 ../../../b2 -q -j2 cxxflags="-fsanitize=undefined -fno-sanitize-recover=undefined" linkflags="-lubsan"  
Performing configuration checks  
  
    - 32-bit                   : no  (cached)  
    - 64-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
    - symlinks supported       : yes (cached)  
...patience...  
...found 2291 targets...  
...updating 20 targets...  
gcc.compile.c++ ../../../bin.v2/libs/format/test/format_test1.test/gcc-6.3.0/debug/format_test1.o  
gcc.compile.c++ ../../../bin.v2/libs/format/test/format_test2.test/gcc-6.3.0/debug/format_test2.o  
gcc.link ../../../bin.v2/libs/format/test/format_test1.test/gcc-6.3.0/debug/format_test1  
testing.capture-output ../../../bin.v2/libs/format/test/format_test1.test/gcc-6.3.0/debug/format_test1.run  
**passed** ../../../bin.v2/libs/format/test/format_test1.test/gcc-6.3.0/debug/format_test1.test  
gcc.link ../../../bin.v2/libs/format/test/format_test2.test/gcc-6.3.0/debug/format_test2  
testing.capture-output ../../../bin.v2/libs/format/test/format_test2.test/gcc-6.3.0/debug/format_test2.run  
**passed** ../../../bin.v2/libs/format/test/format_test2.test/gcc-6.3.0/debug/format_test2.test  
gcc.compile.c++ ../../../bin.v2/libs/format/test/format_test3.test/gcc-6.3.0/debug/format_test3.o  
gcc.compile.c++ ../../../bin.v2/libs/format/test/format_test_wstring.test/gcc-6.3.0/debug/format_test_wstring.o  
gcc.link ../../../bin.v2/libs/format/test/format_test3.test/gcc-6.3.0/debug/format_test3  
testing.capture-output ../../../bin.v2/libs/format/test/format_test3.test/gcc-6.3.0/debug/format_test3.run  
**passed** ../../../bin.v2/libs/format/test/format_test3.test/gcc-6.3.0/debug/format_test3.test  
gcc.link ../../../bin.v2/libs/format/test/format_test_wstring.test/gcc-6.3.0/debug/format_test_wstring  
testing.capture-output ../../../bin.v2/libs/format/test/format_test_wstring.test/gcc-6.3.0/debug/format_test_wstring.run  
**passed** ../../../bin.v2/libs/format/test/format_test_wstring.test/gcc-6.3.0/debug/format_test_wstring.test  
gcc.compile.c++ ../../../bin.v2/libs/format/test/format_test_enum.test/gcc-6.3.0/debug/format_test_enum.o  
gcc.link ../../../bin.v2/libs/format/test/format_test_enum.test/gcc-6.3.0/debug/format_test_enum  
testing.capture-output ../../../bin.v2/libs/format/test/format_test_enum.test/gcc-6.3.0/debug/format_test_enum.run  
**passed** ../../../bin.v2/libs/format/test/format_test_enum.test/gcc-6.3.0/debug/format_test_enum.test  
...updated 20 targets...  
$   
```  
Recommend merging this in for boost 1.66 to remove undefined behavior.

---
