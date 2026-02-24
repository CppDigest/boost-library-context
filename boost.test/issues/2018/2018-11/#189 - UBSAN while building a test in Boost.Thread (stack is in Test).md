# #189 - UBSAN while building a test in Boost.Thread (stack is in Test) [Open]

> Username: jeking3  
> Created at: 2018-11-21 22:34:34 UTC  
> Updated at: 2018-11-21 22:34:34 UTC  
> Url: https://github.com/boostorg/test/issues/189  

Output: https://travis-ci.org/jeking3/thread/jobs/457490782#L2801  
```  
gcc.link ../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept  
testing.capture-output ../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.run  
====== BEGIN OUTPUT ======  
../../boost/test/impl/test_tree.ipp:273:25: runtime error: member call on address 0x7ffcfb300370 which does not point to an object of type 'test_unit_generator'  
0x7ffcfb300370: note: object is of type 'boost::unit_test::ut_detail::template_test_case_gen<test_scoped_lock_concept_invoker, boost::mpl::vector<boost::mutex, boost::timed_mutex, boost::recursive_mutex, boost::recursive_timed_mutex, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na> >'  
 00 00 00 00  98 8c 50 00 00 00 00 00  e0 1b d9 01 00 00 00 00  b0 23 d9 01 00 00 00 00  20 65 b6 9e  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::unit_test::ut_detail::template_test_case_gen<test_scoped_lock_concept_invoker, boost::mpl::vector<boost::mutex, boost::timed_mutex, boost::recursive_mutex, boost::recursive_timed_mutex, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na> >'  
    #0 0x7fbe9e6349b1 in boost::unit_test::test_suite::add(boost::unit_test::test_unit_generator const&, boost::unit_test::decorator::collector_t&) ../../boost/test/impl/test_tree.ipp:273  
    #1 0x7fbe9e637f0f in boost::unit_test::ut_detail::auto_test_unit_registrar::auto_test_unit_registrar(boost::unit_test::test_unit_generator const&, boost::unit_test::decorator::collector_t&) ../../boost/test/impl/test_tree.ipp:509  
    #2 0x40d369 in __static_initialization_and_destruction_0 ../../libs/thread/test/test_lock_concept.cpp:446  
    #3 0x40dab4 in _GLOBAL__sub_I_test_lock_concept.cpp ../../libs/thread/test/test_lock_concept.cpp:575  
    #4 0x4e506c in __libc_csu_init (/home/travis/build/jeking3/boost-root/bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept+0x4e506c)  
    #5 0x7fbe9c8e7ed4 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21ed4)  
    #6 0x40b288  (/home/travis/build/jeking3/boost-root/bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept+0x40b288)  
EXIT STATUS: 1  
====== END OUTPUT ======  
    LD_LIBRARY_PATH="/home/travis/build/jeking3/boost-root/bin.v2/libs/chrono/build/gcc-8/debug/cxxstd-03-iso/threading-multi/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/system/build/gcc-8/debug/cxxstd-03-iso/threading-multi/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/test/build/gcc-8/debug/cxxstd-03-iso/threading-multi/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/thread/build/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/timer/build/gcc-8/debug/cxxstd-03-iso/threading-multi/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept"   > "../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.output" "../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
...failed testing.capture-output ../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.run...  
```  
  
Discovered in Travis CI; Reproducible in the [Boost Docker Development Environment](https://github.com/jeking3/bdde):  
```  
jking@ubuntu:~/boost/libs/thread/test$ bdde  
docker run -v /home/jking/boost:/boost:rw -v /home/jking/bdde:/bdde:ro --workdir /boost/libs/thread/test -it jeking3/bdde:linux /bin/bash  
boost@cbc691077d92:/boost/libs/thread/test$ ubsan test_lock_concept  
UBSAN_OPTIONS=print_stacktrace=1 b2 define=BOOST_NO_STRESS_TEST=1 cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined cxxflags=-fno-sanitize-recover=undefined linkflags=-fsanitize=undefined linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold variant=debug toolset=gcc-8 test_lock_concept  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : yes (cached)  
    - lockfree boost::atomic_flag : yes (cached)  
...patience...  
...patience...  
...found 3533 targets...  
...updating 2 targets...  
testing.capture-output ../../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.run  
====== BEGIN OUTPUT ======  
../../../boost/test/impl/test_tree.ipp:273:25: runtime error: member call on address 0x7ffcf49a96f0 which does not point to an object of type 'test_unit_generator'  
0x7ffcf49a96f0: note: object is of type 'boost::unit_test::ut_detail::template_test_case_gen<test_scoped_lock_concept_invoker, boost::mpl::vector<boost::mutex, boost::timed_mutex, boost::recursive_mutex, boost::recursive_timed_mutex, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na> >'  
 11 56 00 00  68 1a 55 76 11 56 00 00  10 f8 9f 77 11 56 00 00  50 00 a0 77 11 56 00 00  04 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::unit_test::ut_detail::template_test_case_gen<test_scoped_lock_concept_invoker, boost::mpl::vector<boost::mutex, boost::timed_mutex, boost::recursive_mutex, boost::recursive_timed_mutex, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na> >'  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/boost/bin.v2/libs/chrono/build/gcc-8/debug/threading-multi/visibility-hidden:/boost/bin.v2/libs/system/build/gcc-8/debug/threading-multi/visibility-hidden:/boost/bin.v2/libs/test/build/gcc-8/debug/threading-multi/visibility-hidden:/boost/bin.v2/libs/thread/build/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden:/boost/bin.v2/libs/timer/build/gcc-8/debug/threading-multi/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept"   > "../../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.output" "../../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../bin.v2/libs/thread/test/test_lock_concept.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_lock_concept.run...  
...failed updating 1 target...  
```
