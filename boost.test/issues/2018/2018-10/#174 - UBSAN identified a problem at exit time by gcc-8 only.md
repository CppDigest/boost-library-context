# #174 - UBSAN identified a problem at exit time by gcc-8 only [Closed]

> Username: jeking3  
> Created at: 2018-10-29 18:43:54 UTC  
> Updated at: 2019-02-08 17:25:42 UTC  
> Closed at: 2019-02-02 12:46:32 UTC  
> Url: https://github.com/boostorg/test/issues/174  

During a build of Boost.Rational with UBSAN and gcc-8 - this issue is reproducible in https://github.com/boostorg/rational/pull/30.  
```  
testing.capture-output ../../bin.v2/libs/rational/test/rational_test.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/rational_test.run  
====== BEGIN OUTPUT ======  
Running tests for boost::rational<long>  
Implementation issue: the minimal size for a rational  
is twice the size of the underlying integer type.  
Checking to see if space is being wasted.  
	sizeof(long) == 8  
	sizeof(boost::rational<long>) == 16  
Implementation has minimal size  
Running 83 test cases...  
*** No errors detected  
../../boost/test/impl/framework.ipp:839:34: runtime error: member call on address 0x000000b5caa0 which does not point to an object of type 'test_observer'  
0x000000b5caa0: note: object has invalid vptr  
 00 00 00 00  00 00 00 00 00 00 00 00  01 00 00 00 00 00 00 00  d8 cb b5 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              invalid vptr  
    #0 0x6656bb in boost::unit_test::framework::state::priority_order::operator()(boost::unit_test::test_observer*, boost::unit_test::test_observer*) const ../../boost/test/impl/framework.ipp:839  
    #1 0x695d5d in std::_Rb_tree<boost::unit_test::test_observer*, boost::unit_test::test_observer*, std::_Identity<boost::unit_test::test_observer*>, boost::unit_test::framework::state::priority_order, std::allocator<boost::unit_test::test_observer*> >::_M_lower_bound(std::_Rb_tree_node<boost::unit_test::test_observer*>*, std::_Rb_tree_node_base*, boost::unit_test::test_observer* const&) (/home/travis/build/boostorg/boost-root/bin.v2/libs/rational/test/rational_test.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/rational_test+0x695d5d)  
    #2 0x68db46 in std::_Rb_tree<boost::unit_test::test_observer*, boost::unit_test::test_observer*, std::_Identity<boost::unit_test::test_observer*>, boost::unit_test::framework::state::priority_order, std::allocator<boost::unit_test::test_observer*> >::equal_range(boost::unit_test::test_observer* const&) /usr/include/c++/8/bits/stl_tree.h:1966  
    #3 0x67dea5 in std::_Rb_tree<boost::unit_test::test_observer*, boost::unit_test::test_observer*, std::_Identity<boost::unit_test::test_observer*>, boost::unit_test::framework::state::priority_order, std::allocator<boost::unit_test::test_observer*> >::erase(boost::unit_test::test_observer* const&) /usr/include/c++/8/bits/stl_tree.h:2516  
    #4 0x66f666 in std::set<boost::unit_test::test_observer*, boost::unit_test::framework::state::priority_order, std::allocator<boost::unit_test::test_observer*> >::erase(boost::unit_test::test_observer* const&) /usr/include/c++/8/bits/stl_set.h:685  
    #5 0x64662d in boost::unit_test::framework::deregister_observer(boost::unit_test::test_observer&) ../../boost/test/impl/framework.ipp:1343  
    #6 0x6e3d95 in boost::unit_test::global_configuration::~global_configuration() ../../boost/test/impl/test_tree.ipp:556  
    #7 0x4db2b4 in ~global_configuration_impl ../../boost/test/tree/global_fixture.hpp:68  
    #8 0x7fe46ef1e1a8  (/lib/x86_64-linux-gnu/libc.so.6+0x3c1a8)  
    #9 0x7fe46ef1e1f4 in exit (/lib/x86_64-linux-gnu/libc.so.6+0x3c1f4)  
    #10 0x7fe46ef03f4b in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21f4b)  
    #11 0x40a008  (/home/travis/build/boostorg/boost-root/bin.v2/libs/rational/test/rational_test.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/rational_test+0x40a008)  
EXIT STATUS: 1  
====== END OUTPUT ======  
    LD_LIBRARY_PATH="/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/rational/test/rational_test.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/rational_test"   > "../../bin.v2/libs/rational/test/rational_test.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/rational_test.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/rational/test/rational_test.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/rational_test.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/rational/test/rational_test.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/rational_test.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/rational/test/rational_test.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/rational_test.output" "../../bin.v2/libs/rational/test/rational_test.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/rational_test.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/rational/test/rational_test.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/rational_test.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
...failed testing.capture-output ../../bin.v2/libs/rational/test/rational_test.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/rational_test.run...  
```  
see: https://travis-ci.org/boostorg/rational/jobs/447297672

---

## Comment 1

> Username: jeking3  
> Created at: 2018-10-30 01:15:18 UTC  
> Url: https://github.com/boostorg/test/issues/174#issuecomment-434136772  

Re-ran with clang-6, gcc-7, and gcc-8 and only the gcc-8 UBSAN triggers this failure.  
https://travis-ci.org/boostorg/rational/builds/448008702

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2018-10-30 08:13:22 UTC  
> Updated at: 2018-10-30 08:13:58 UTC  
> Url: https://github.com/boostorg/test/issues/174#issuecomment-434208892  

I think I know why we have this UB, but the fix is far from easy (has again something to do with global variables init order).  
I am afraid I will not be able to fix this anytime soon, certainly not for 1.69

---

## Comment 3

> Username: jeking3  
> Created at: 2018-11-03 14:32:39 UTC  
> Url: https://github.com/boostorg/test/issues/174#issuecomment-435592419  

Another case: https://travis-ci.org/boostorg/rational/jobs/450100503

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2018-11-09 22:03:48 UTC  
> Url: https://github.com/boostorg/test/issues/174#issuecomment-437509169  

Hi, I have a potential fix on this branch: `topic/GH-174-undefined-behaviour-on-exit`. Would it be possible for you to try UBsan on this one? I do not know how to set it up with the build, would you please point me to something that integrates UBsan with boost.build?  
  
Thanks!

---

## Comment 5

> Username: jeking3  
> Created at: 2018-11-09 22:14:51 UTC  
> Updated at: 2018-11-09 22:19:42 UTC  
> Url: https://github.com/boostorg/test/issues/174#issuecomment-437511682  

If you have gcc-7 or gcc-8 available you can run UBSAN yourself like this:  
  
$ UBSAN_OPTIONS=print_stacktrace=1 ../../../b2 cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined cxxflags=-fno-sanitize-recover=undefined linkflags=-fsanitize=undefined linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold variant=debug  
  
This is pretty much what the Travis CI build jobs in [boost-ci](https://github.com/boostorg/boost-ci) do.  In any case I will start up a CI build from that branch with my CI changes on top.  
  
Here's the UBSAN CI job:  
  
https://travis-ci.org/jeking3/test/jobs/453094905

---

## Comment 6

> Username: jeking3  
> Created at: 2018-11-09 23:01:02 UTC  
> Url: https://github.com/boostorg/test/issues/174#issuecomment-437522306  

Given the relative size of boost::test runs, the standard boost-ci template will beed to be trimmed.  We cannot run all the language levels for example.

---

## Comment 7

> Username: jeking3  
> Created at: 2018-11-09 23:02:53 UTC  
> Url: https://github.com/boostorg/test/issues/174#issuecomment-437522710  

I see this in the logs:  
```  
gcc.compile.c++ ../../bin.v2/libs/test/test/assertion-construction-test.test/gcc-8/debug/cxxstd-11-iso/visibility-hidden/writing-test-ts/assertion-construction-test.o  
In file included from /usr/include/c++/8/map:60,  
                 from ../../boost/test/tree/test_unit.hpp:35,  
                 from ../../boost/test/tree/auto_registration.hpp:21,  
                 from ../../boost/test/unit_test_suite.hpp:17,  
                 from ../../boost/test/unit_test.hpp:19,  
                 from ../../libs/test/test/writing-test-ts/assertion-construction-test.cpp:17:  
/usr/include/c++/8/bits/stl_tree.h: In instantiation of â€˜class std::_Rb_tree<int, int, std::_Identity<int>, int, std::allocator<int> >â€™:  
/usr/include/c++/8/bits/stl_set.h:133:17:   required from â€˜class std::set<int, int>â€™  
../../boost/test/utils/is_forward_iterable.hpp:108:39:   required by substitution of â€˜template<class U> static decltype (declval<U>().size()) boost::unit_test::ut_detail::has_member_size<std::set<int, int> >::test<U>(U*) [with U = std::set<int, int>]â€™  
../../boost/test/utils/is_forward_iterable.hpp:112:62:   required from â€˜const bool boost::unit_test::ut_detail::has_member_size<std::set<int, int> >::valueâ€™  
../../libs/test/test/writing-test-ts/assertion-construction-test.cpp:127:5:   required from here  
/usr/include/c++/8/bits/stl_tree.h:452:21: error: static assertion failed: comparison object must be invocable with two arguments of key type  
       static_assert(__is_invocable<_Compare&, const _Key&, const _Key&>{},  
                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../../boost/test/tools/old/impl.hpp:20,  
                 from ../../boost/test/test_tools.hpp:46,  
                 from ../../boost/test/unit_test.hpp:18,  
                 from ../../libs/test/test/writing-test-ts/assertion-construction-test.cpp:17:  
../../boost/test/tools/assertion_result.hpp: In instantiation of â€˜boost::test_tools::assertion_result::assertion_result(const BoolConvertable&) [with BoolConvertable = char [4]]â€™:  
../../boost/test/tools/assertion.hpp:301:26:   required from â€˜boost::test_tools::assertion_result boost::test_tools::assertion::value_expr<T>::evaluate(bool) const [with T = const char (&)[4]]â€™  
../../libs/test/test/writing-test-ts/assertion-construction-test.cpp:266:67:   required from here  
../../boost/test/tools/assertion_result.hpp:57:74: warning: the compiler can assume that the address of â€˜pv_â€™ will never be NULL [-Waddress]  
     assertion_result( BoolConvertable const& pv_ ) : p_predicate_value( !!pv_ ) {}  
                                                                          ^~~~  
In file included from ../../boost/test/test_tools.hpp:53,  
                 from ../../boost/test/unit_test.hpp:18,  
                 from ../../libs/test/test/writing-test-ts/assertion-construction-test.cpp:17:  
../../boost/test/tools/assertion.hpp: In instantiation of â€˜static boost::test_tools::assertion::op::EQ<Lhs, Rhs, Enabler>::result_type boost::test_tools::assertion::op::EQ<Lhs, Rhs, Enabler>::eval(const Lhs&, const Rhs&) [with Lhs = long unsigned int; Rhs = int; Enabler = void; boost::test_tools::assertion::op::EQ<Lhs, Rhs, Enabler>::result_type = bool]â€™:  
../../boost/test/tools/assertion.hpp:354:24:   required from â€˜boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::result_type boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::value() const [with LExpr = boost::test_tools::assertion::value_expr<long unsigned int>; Rhs = int; OP = boost::test_tools::assertion::op::EQ<long unsigned int, int, void>; boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::result_type = bool]â€™  
../../boost/test/tools/assertion.hpp:363:42:   required from â€˜boost::test_tools::assertion_result boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::evaluate(bool) const [with LExpr = boost::test_tools::assertion::value_expr<long unsigned int>; Rhs = int; OP = boost::test_tools::assertion::op::EQ<long unsigned int, int, void>]â€™  
../../libs/test/test/writing-test-ts/assertion-construction-test.cpp:195:5:   required from here  
../../boost/test/tools/assertion.hpp:72:13: warning: comparison of integer expressions of different signedness: â€˜const long unsigned intâ€™ and â€˜const intâ€™ [-Wsign-compare]  
     action( ==, EQ, != )                    \  
             ^  
../../boost/test/tools/assertion.hpp:144:20: note: in definition of macro â€˜DEFINE_CONST_OPERâ€™  
         return lhs oper rhs;                        \  
                    ^~~~  
../../boost/test/tools/assertion.hpp:93:5: note: in expansion of macro â€˜BOOST_TEST_FOR_EACH_COMP_OPâ€™  
     BOOST_TEST_FOR_EACH_COMP_OP(action)     \  
     ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
../../boost/test/tools/assertion.hpp:163:1: note: in expansion of macro â€˜BOOST_TEST_FOR_EACH_CONST_OPâ€™  
 BOOST_TEST_FOR_EACH_CONST_OP( DEFINE_CONST_OPER )  
 ^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
    "g++-8"   -std=c++11 -fvisibility-inlines-hidden -fPIC -m64 -O0 -fno-inline -Wall -pedantic -g -fvisibility=hidden -Wno-long-long -Wno-variadic-macros -fno-omit-frame-pointer -fno-sanitize-recover=undefined -fsanitize=undefined -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_CHRONO_THREAD_DISABLED -DBOOST_NO_STRESS_TEST=1 -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_TIMER_STATIC_LINK=1  -I"../.." -c -o "../../bin.v2/libs/test/test/assertion-construction-test.test/gcc-8/debug/cxxstd-11-iso/visibility-hidden/writing-test-ts/assertion-construction-test.o" "../../libs/test/test/writing-test-ts/assertion-construction-test.cpp"  
  
...failed gcc.compile.c++ ../../bin.v2/libs/test/test/assertion-construction-test.test/gcc-8/debug/cxxstd-11-iso/visibility-hidden/writing-test-ts/assertion-construction-test.o...  
```  
and C++14 too:  
```  
gcc.compile.c++ ../../bin.v2/libs/test/test/assertion-construction-test.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/writing-test-ts/assertion-construction-test.o  
In file included from /usr/include/c++/8/map:60,  
                 from ../../boost/test/tree/test_unit.hpp:35,  
                 from ../../boost/test/tree/auto_registration.hpp:21,  
                 from ../../boost/test/unit_test_suite.hpp:17,  
                 from ../../boost/test/unit_test.hpp:19,  
                 from ../../libs/test/test/writing-test-ts/assertion-construction-test.cpp:17:  
/usr/include/c++/8/bits/stl_tree.h: In instantiation of â€˜class std::_Rb_tree<int, int, std::_Identity<int>, int, std::allocator<int> >â€™:  
/usr/include/c++/8/bits/stl_set.h:133:17:   required from â€˜class std::set<int, int>â€™  
../../boost/test/utils/is_forward_iterable.hpp:108:39:   required by substitution of â€˜template<class U> static decltype (declval<U>().size()) boost::unit_test::ut_detail::has_member_size<std::set<int, int> >::test<U>(U*) [with U = std::set<int, int>]â€™  
../../boost/test/utils/is_forward_iterable.hpp:112:62:   required from â€˜const bool boost::unit_test::ut_detail::has_member_size<std::set<int, int> >::valueâ€™  
../../libs/test/test/writing-test-ts/assertion-construction-test.cpp:127:5:   required from here  
/usr/include/c++/8/bits/stl_tree.h:452:21: error: static assertion failed: comparison object must be invocable with two arguments of key type  
       static_assert(__is_invocable<_Compare&, const _Key&, const _Key&>{},  
                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../../boost/test/tools/old/impl.hpp:20,  
                 from ../../boost/test/test_tools.hpp:46,  
                 from ../../boost/test/unit_test.hpp:18,  
                 from ../../libs/test/test/writing-test-ts/assertion-construction-test.cpp:17:  
../../boost/test/tools/assertion_result.hpp: In instantiation of â€˜boost::test_tools::assertion_result::assertion_result(const BoolConvertable&) [with BoolConvertable = char [4]]â€™:  
../../boost/test/tools/assertion.hpp:301:26:   required from â€˜boost::test_tools::assertion_result boost::test_tools::assertion::value_expr<T>::evaluate(bool) const [with T = const char (&)[4]]â€™  
../../libs/test/test/writing-test-ts/assertion-construction-test.cpp:266:67:   required from here  
../../boost/test/tools/assertion_result.hpp:57:74: warning: the compiler can assume that the address of â€˜pv_â€™ will never be NULL [-Waddress]  
     assertion_result( BoolConvertable const& pv_ ) : p_predicate_value( !!pv_ ) {}  
                                                                          ^~~~  
In file included from ../../boost/test/test_tools.hpp:53,  
                 from ../../boost/test/unit_test.hpp:18,  
                 from ../../libs/test/test/writing-test-ts/assertion-construction-test.cpp:17:  
../../boost/test/tools/assertion.hpp: In instantiation of â€˜static boost::test_tools::assertion::op::EQ<Lhs, Rhs, Enabler>::result_type boost::test_tools::assertion::op::EQ<Lhs, Rhs, Enabler>::eval(const Lhs&, const Rhs&) [with Lhs = long unsigned int; Rhs = int; Enabler = void; boost::test_tools::assertion::op::EQ<Lhs, Rhs, Enabler>::result_type = bool]â€™:  
../../boost/test/tools/assertion.hpp:354:24:   required from â€˜boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::result_type boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::value() const [with LExpr = boost::test_tools::assertion::value_expr<long unsigned int>; Rhs = int; OP = boost::test_tools::assertion::op::EQ<long unsigned int, int, void>; boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::result_type = bool]â€™  
../../boost/test/tools/assertion.hpp:363:42:   required from â€˜boost::test_tools::assertion_result boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::evaluate(bool) const [with LExpr = boost::test_tools::assertion::value_expr<long unsigned int>; Rhs = int; OP = boost::test_tools::assertion::op::EQ<long unsigned int, int, void>]â€™  
../../libs/test/test/writing-test-ts/assertion-construction-test.cpp:195:5:   required from here  
../../boost/test/tools/assertion.hpp:72:13: warning: comparison of integer expressions of different signedness: â€˜const long unsigned intâ€™ and â€˜const intâ€™ [-Wsign-compare]  
     action( ==, EQ, != )                    \  
             ^  
../../boost/test/tools/assertion.hpp:144:20: note: in definition of macro â€˜DEFINE_CONST_OPERâ€™  
         return lhs oper rhs;                        \  
                    ^~~~  
../../boost/test/tools/assertion.hpp:93:5: note: in expansion of macro â€˜BOOST_TEST_FOR_EACH_COMP_OPâ€™  
     BOOST_TEST_FOR_EACH_COMP_OP(action)     \  
     ^~~~~~~~~~~~~~~~~~~~~~~~~~~  
../../boost/test/tools/assertion.hpp:163:1: note: in expansion of macro â€˜BOOST_TEST_FOR_EACH_CONST_OPâ€™  
 BOOST_TEST_FOR_EACH_CONST_OP( DEFINE_CONST_OPER )  
 ^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  
    "g++-8"   -std=c++14 -fvisibility-inlines-hidden -fPIC -m64 -O0 -fno-inline -Wall -pedantic -g -fvisibility=hidden -Wno-long-long -Wno-variadic-macros -fno-omit-frame-pointer -fno-sanitize-recover=undefined -fsanitize=undefined -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_CHRONO_THREAD_DISABLED -DBOOST_NO_STRESS_TEST=1 -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_TIMER_STATIC_LINK=1  -I"../.." -c -o "../../bin.v2/libs/test/test/assertion-construction-test.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/writing-test-ts/assertion-construction-test.o" "../../libs/test/test/writing-test-ts/assertion-construction-test.cpp"  
  
...failed gcc.compile.c++ ../../bin.v2/libs/test/test/assertion-construction-test.test/gcc-8/debug/cxxstd-14-iso/visibility-hidden/writing-test-ts/assertion-construction-test.o...  
```  
looks like there's still an issue in here but maybe different.

---

## Comment 8

> Username: jeking3  
> Created at: 2018-11-09 23:08:29 UTC  
> Url: https://github.com/boostorg/test/issues/174#issuecomment-437523822  

For MSVC and C++17 modes:  
```  
compile-c-c++ bin.v2\libs\test\example\unit_test_example_12.test\msvc-14.1\release\address-model-64\cxxstd-latest-iso\threading-multi\unit_test_example_12.obj  
unit_test_example_12.cpp  
libs\test\example\unit_test_example_12.cpp(68): error C2039: 'bind1st': is not a member of 'std'  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.15.26726\include\map(20): note: see declaration of 'std'  
libs\test\example\unit_test_example_12.cpp(61): note: while compiling class template member function 'unsigned long hash_function<4>::operator ()(const std::string &)'  
libs\test\example\unit_test_example_12.cpp(128): note: see reference to function template instantiation 'unsigned long hash_function<4>::operator ()(const std::string &)' being compiled  
libs\test\example\unit_test_example_12.cpp(138): note: see reference to class template instantiation 'hash_function<4>' being compiled  
.\boost/bind/placeholders.hpp(54): note: see reference to class template instantiation 'boost::arg<9>' being compiled  
.\boost/bind/placeholders.hpp(53): note: see reference to class template instantiation 'boost::arg<8>' being compiled  
.\boost/bind/placeholders.hpp(52): note: see reference to class template instantiation 'boost::arg<7>' being compiled  
.\boost/bind/placeholders.hpp(51): note: see reference to class template instantiation 'boost::arg<6>' being compiled  
.\boost/bind/placeholders.hpp(50): note: see reference to class template instantiation 'boost::arg<5>' being compiled  
.\boost/bind/placeholders.hpp(49): note: see reference to class template instantiation 'boost::arg<4>' being compiled  
.\boost/bind/placeholders.hpp(48): note: see reference to class template instantiation 'boost::arg<3>' being compiled  
.\boost/bind/placeholders.hpp(47): note: see reference to class template instantiation 'boost::arg<2>' being compiled  
.\boost/bind/placeholders.hpp(46): note: see reference to class template instantiation 'boost::arg<1>' being compiled  
    call "bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"bin.v2\libs\test\example\unit_test_example_12.test\msvc-14.1\release\address-model-64\cxxstd-latest-iso\threading-multi\unit_test_example_12.obj.rsp"   
...failed compile-c-c++ bin.v2\libs\test\example\unit_test_example_12.test\msvc-14.1\release\address-model-64\cxxstd-latest-iso\threading-multi\unit_test_example_12.obj...  
```  
  
When you have something else to test let me know and I can run it again with a reduced set of build matrices that might actually complete.  If we get it all working I will submit a PR into Boost.Test.

---

## Comment 9

> Username: raffienficiaud  
> Created at: 2018-11-10 02:04:41 UTC  
> Url: https://github.com/boostorg/test/issues/174#issuecomment-437549630  

Thanks for checking this. The branch I am pointing to you is green on my CI (gcc5 c++03/c++11, apple clang Xcode 8 c++03/c++11 and VS 2008,12,13 and 17). I do not have any sanitizer activated because I do not know how to set this up :) I will follow your command line soonish.  
  
From the logs https://travis-ci.org/jeking3/test/jobs/453094905, I can see very strange things:  
  
```  
====== BEGIN OUTPUT ======  
../../boost/test/impl/unit_test_log.ipp:451:134: runtime error: member call on address 0x7fff9c276b00 which does not point to an object of type 'lazy_ostream'  
0x7fff9c276b00: note: object is of type 'boost::unit_test::lazy_ostream_impl<boost::unit_test::lazy_ostream, boost::unit_test::basic_cstring<char const>, boost::unit_test::basic_cstring<char const> const&>'  
 b0 7f 00 00  e0 ec 40 00 00 00 00 00  00 c1 40 00 00 00 00 00  70 51 41 00 00 00 00 00  c0 6a 27 9c  
```  
while the definition is `class lazy_ostream_impl : public lazy_ostream` and the access raising the error is a ref on `lazy_ostream`.  So I do not understand.  
  
There are also other things about the exceptions that are not being caught by boost.test and which I do not understand neither:  
```  
====== BEGIN OUTPUT ======  
Running 1 test case...  
../../libs/test/example/unit_test_example_02.cpp(20): error: in "free_test_function": check 2 == 1 has failed  
../../libs/test/example/unit_test_example_02.cpp:25:5: runtime error: load of misaligned address 0x000000000001 for type 'int', which requires 4 byte alignment  
0x000000000001: note: pointer points here  
<memory cannot be printed>  
    #0 0x406912 in free_test_function() ../../libs/test/example/unit_test_example_02.cpp:25  
    #1 0x409871 in boost::detail::function::void_function_invoker<void (*)(), void>::invoke(boost::detail::function::function_buffer&) (/home/travis/build/jeking3/boost-root/bin.v2/libs/test/example/unit_test_example_02.test/gcc-8/debug/cxxstd-03-iso/visibility-hidden/unit_test_example_02+0x409871)  
    #2 0x4364cd in boost::function0<void>::operator()() const ../../boost/function/function_template.hpp:829  
    #3 0x5df84a in boost::detail::forward::operator()() ../../boost/test/impl/execution_monitor.ipp:1312  
    #4 0x5e61b0 in boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) ../../boost/function/function_template.hpp:172  
    #5 0x5e3f65 in boost::function0<int>::operator()() const ../../boost/function/function_template.hpp:829  
    #6 0x5e1550 in int boost::detail::do_invoke<boost::shared_ptr<boost::detail::translator_holder_base>, boost::function<int ()> >(boost::shared_ptr<boost::detail::translator_holder_base> const&, boost::function<int ()> const&) ../../boost/test/impl/execution_monitor.ipp:286  
    #7 0x5d7a18 in boost::execution_monitor::catch_signals(boost::function<int ()> const&) ../../boost/test/impl/execution_monitor.ipp:875  
    #8 0x5d7f08 in boost::execution_monitor::execute(boost::function<int ()> const&) ../../boost/test/impl/execution_monitor.ipp:1214  
```  
  
I am not running the tests in the example folder, which explains why I am not seeing the VS errors.  
I am also activating all the warnings as errors for the library but not for the tests themselves (too much work to fix them all), which may explain why you have so many failures. The warnings should be silenced by specific headers for the library itself, problem is that compilers may have different set of warnings that are not covered. This is a recent change,   
  
Something like  
```  
/usr/include/c++/8/bits/stl_tree.h:452:21: error: static assertion failed: comparison object must be invocable with two arguments of key type  
       static_assert(__is_invocable<_Compare&, const _Key&, const _Key&>{},  
                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
is I believe a compiler issue, see https://github.com/boostorg/test/issues/152 .  
  
Do you have other tests environment where the UBsan issue was visible? Otherwise I will configure/run it on my side when I have time.

---

## Comment 10

> Username: jeking3  
> Created at: 2018-11-11 22:39:35 UTC  
> Url: https://github.com/boostorg/test/issues/174#issuecomment-437711991  

I ran the change through CI and I did not see the issues I originally reported, so I would say the issue was resolved by the changes.

---

## Comment 11

> Username: raffienficiaud  
> Created at: 2019-02-02 12:46:32 UTC  
> Url: https://github.com/boostorg/test/issues/174#issuecomment-459962493  

Solved via 1f88919c6b7bf30b75c3bd20025c3b64e7420f19 and 1e38868908fa995f984f70771ad0da2ea5bc08e1
