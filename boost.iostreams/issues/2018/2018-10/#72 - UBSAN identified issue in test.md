# #72 - UBSAN identified issue in test [Open]

> Username: jeking3  
> Created at: 2018-10-31 12:20:54 UTC  
> Updated at: 2018-10-31 12:20:54 UTC  
> Url: https://github.com/boostorg/iostreams/issues/72  

https://travis-ci.org/boostorg/iostreams/jobs/448302953#L1170  
```  
testing.capture-output ../../bin.v2/libs/iostreams/test/code_converter_test.test/gcc-7/debug/cxxstd-03-iso/visibility-hidden/code_converter_test.run  
====== BEGIN OUTPUT ======  
../../libs/iostreams/test/code_converter_test.cpp:205:5: runtime error: member call on address 0x7ffd672455d0 which does not point to an object of type '__codecvt_abstract_base'  
0x7ffd672455d0: note: object is of type 'utf8_codecvt_facet<wchar_t, char>'  
 00 00 00 00  40 9a 6a 00 00 00 00 00  00 00 00 00 00 00 00 00  00 84 ba 66 bb 7f 00 00  c3 3c dd 66  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'utf8_codecvt_facet<wchar_t, char>'  
    #0 0x41cc18 in bool codecvt_test1<utf8_codecvt_facet<wchar_t, char> >() (/home/travis/build/boostorg/boost-root/bin.v2/libs/iostreams/test/code_converter_test.test/gcc-7/debug/cxxstd-03-iso/visibility-hidden/code_converter_test+0x41cc18)  
    #1 0x419b54 in bool codecvt_test<utf8_codecvt_facet<wchar_t, char> >() (/home/travis/build/boostorg/boost-root/bin.v2/libs/iostreams/test/code_converter_test.test/gcc-7/debug/cxxstd-03-iso/visibility-hidden/code_converter_test+0x419b54)  
    #2 0x40f02a in code_converter_test() ../../libs/iostreams/test/code_converter_test.cpp:254  
    #3 0x43646e in boost::detail::function::void_function_invoker0<void (*)(), void>::invoke(boost::detail::function::function_buffer&) (/home/travis/build/boostorg/boost-root/bin.v2/libs/iostreams/test/code_converter_test.test/gcc-7/debug/cxxstd-03-iso/visibility-hidden/code_converter_test+0x43646e)  
    #4 0x5d1d68 in boost::function0<void>::operator()() const ../../boost/function/function_template.hpp:829  
    #5 0x61f1ba in boost::detail::forward::operator()() ../../boost/test/impl/execution_monitor.ipp:1312  
    #6 0x624929 in boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) ../../boost/function/function_template.hpp:172  
    #7 0x622c7e in boost::function0<int>::operator()() const ../../boost/function/function_template.hpp:830  
    #8 0x620870 in int boost::detail::do_invoke<boost::shared_ptr<boost::detail::translator_holder_base>, boost::function<int ()> >(boost::shared_ptr<boost::detail::translator_holder_base> const&, boost::function<int ()> const&) ../../boost/test/impl/execution_monitor.ipp:286  
    #9 0x618c7b in boost::execution_monitor::catch_signals(boost::function<int ()> const&) ../../boost/test/impl/execution_monitor.ipp:875  
    #10 0x618f6c in boost::execution_monitor::execute(boost::function<int ()> const&) ../../boost/test/impl/execution_monitor.ipp:1214  
    #11 0x61afd0 in boost::execution_monitor::vexecute(boost::function<void ()> const&) ../../boost/test/impl/execution_monitor.ipp:1321  
    #12 0x60da09 in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned int) ../../boost/test/impl/unit_test_monitor.ipp:49  
    #13 0x5cf928 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) ../../boost/test/impl/framework.ipp:789  
    #14 0x5cefc4 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) ../../boost/test/impl/framework.ipp:737  
    #15 0x5cefc4 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) ../../boost/test/impl/framework.ipp:737  
    #16 0x5bf706 in boost::unit_test::framework::run(unsigned long, bool) ../../boost/test/impl/framework.ipp:1631  
    #17 0x4fbe3d in boost::unit_test::unit_test_main(boost::unit_test::test_suite* (*)(int, char**), int, char**) ../../boost/test/impl/unit_test_main.ipp:247  
    #18 0x4fcba0 in main ../../boost/test/impl/unit_test_main.ipp:303  
    #19 0x7fbb66809f44 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21f44)  
    #20 0x40c408  (/home/travis/build/boostorg/boost-root/bin.v2/libs/iostreams/test/code_converter_test.test/gcc-7/debug/cxxstd-03-iso/visibility-hidden/code_converter_test+0x40c408)  
EXIT STATUS: 1  
====== END OUTPUT ======  
    LD_LIBRARY_PATH="/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/iostreams/test/code_converter_test.test/gcc-7/debug/cxxstd-03-iso/visibility-hidden/code_converter_test"   > "../../bin.v2/libs/iostreams/test/code_converter_test.test/gcc-7/debug/cxxstd-03-iso/visibility-hidden/code_converter_test.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/iostreams/test/code_converter_test.test/gcc-7/debug/cxxstd-03-iso/visibility-hidden/code_converter_test.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/iostreams/test/code_converter_test.test/gcc-7/debug/cxxstd-03-iso/visibility-hidden/code_converter_test.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/iostreams/test/code_converter_test.test/gcc-7/debug/cxxstd-03-iso/visibility-hidden/code_converter_test.output" "../../bin.v2/libs/iostreams/test/code_converter_test.test/gcc-7/debug/cxxstd-03-iso/visibility-hidden/code_converter_test.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/iostreams/test/code_converter_test.test/gcc-7/debug/cxxstd-03-iso/visibility-hidden/code_converter_test.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
...failed testing.capture-output ../../bin.v2/libs/iostreams/test/code_converter_test.test/gcc-7/debug/cxxstd-03-iso/visibility-hidden/code_converter_test.run...  
```  
Marking the test as a known failure.
