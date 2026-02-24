# #135 - integer overflow (UBSAN) [Closed]

> Username: jeking3  
> Created at: 2018-07-24 10:48:58 UTC  
> Updated at: 2018-07-30 11:49:33 UTC  
> Closed at: 2018-07-30 11:49:32 UTC  
> Url: https://github.com/boostorg/math/issues/135  

Build environment: https://github.com/boostorg/boost/pull/184 (docker)  
  
```  
boost@a7d451ec0735:/boost/libs/math/test$ UBSAN_OPTIONS=print_stacktrace=1 ../../../b2 toolset=gcc-7 cxxstd=03 cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined cxxflags=-fno-sanitize-recover=undefined linkflags=-fsanitize=undefined linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold variant=debug -q -a  
  
...  
  
testing.capture-output ../../../bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
Testing type cpp_dec_float_50  
Denormals are flushed to zero.  
Testing type cpp_dec_float_50 with initial value 1.34567e+22  
Testing type cpp_dec_float_50 with initial value -1.34567e+22  
Testing type cpp_dec_float_50 with initial value 1.34567e-22  
Testing type cpp_dec_float_50 with initial value -1.34567e-22  
Testing type cpp_dec_float_50 with initial value 4.48557e-23  
Testing type cpp_dec_float_50 with initial value -4.48557e-23  
Testing type cpp_dec_float_50 with initial value 1e-49  
Testing type cpp_dec_float_50 with initial value -1e-49  
Testing type cpp_dec_float_50 with initial value 1e-67108864  
../../../boost/math/special_functions/next.hpp:76:72: runtime error: signed integer overflow: 49 - -2147483648 cannot be represented in type 'int'  
    #0 0x557eb0c2c6ba in boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u, int, void>, (boost::multiprecision::expression_template_option)1> boost::math::detail::normalize_value<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u, int, void>, (boost::multiprecision::expression_template_option)1> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u, int, void>, (boost::multiprecision::expression_template_option)1> const&, mpl_::bool_<true> const&) (/boost/bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal+0x1db6ba)  
    #1 0x557eb0c2e0e3 in boost::math::tools::promote_args<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u, int, void>, (boost::multiprecision::expression_template_option)1>, float, float, float, float>::type boost::math::float_distance<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) (/boost/bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal+0x1dd0e3)  
    #2 0x557eb0c32da4 in void test_value<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u, int, void>, (boost::multiprecision::expression_template_option)1> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u, int, void>, (boost::multiprecision::expression_template_option)1> const&, char const*) (/boost/bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal+0x1e1da4)  
    #3 0x557eb0c3960e in void test_values<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u, int, void>, (boost::multiprecision::expression_template_option)1> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50u, int, void>, (boost::multiprecision::expression_template_option)1> const&, char const*) (/boost/bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal+0x1e860e)  
    #4 0x557eb0bf36f1 in test_main_invoker() (/boost/bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal+0x1a26f1)  
    #5 0x557eb0d9298b in boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) (/boost/bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal+0x34198b)  
    #6 0x557eb0d90084 in boost::execution_monitor::catch_signals(boost::function<int ()> const&) (/boost/bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal+0x33f084)  
    #7 0x557eb0d9043a in boost::execution_monitor::execute(boost::function<int ()> const&) (/boost/bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal+0x33f43a)  
    #8 0x557eb0d921d9 in boost::execution_monitor::vexecute(boost::function<void ()> const&) (/boost/bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal+0x3411d9)  
    #9 0x557eb0d81e15 in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned int) (/boost/bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal+0x330e15)  
    #10 0x557eb0d67ef1 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) (/boost/bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal+0x316ef1)  
    #11 0x557eb0d68952 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) (/boost/bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal+0x317952)  
    #12 0x557eb0d505f2 in boost::unit_test::framework::run(unsigned long, bool) (/boost/bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal+0x2ff5f2)  
    #13 0x557eb0c9d23b in boost::unit_test::unit_test_main(boost::unit_test::test_suite* (*)(int, char**), int, char**) (/boost/bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal+0x24c23b)  
    #14 0x7f796e36eb96 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21b96)  
    #15 0x557eb0bf15d9 in _start (/boost/bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal+0x1a05d9)  
  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal"   > "../../../bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal.output" "../../../bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../bin.v2/libs/math/test/test_next_decimal.test/gcc-7/release/cxxstd-03-iso/link-static/test_next_decimal.run...  
...failed updating 1 target...  
...skipped 1 target...  
...updated 973 targets...  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-07-28 17:46:28 UTC  
> Url: https://github.com/boostorg/math/issues/135#issuecomment-408624117  

I think I need some help with this as I see a different issue (in gcc-7 and 8) which appears to be spurious and does not occur with clang-6.  Certainly the location of the crash makes no logical sense, so I'm stuck at this point.  Steps to reproduce:  
  
1) This test is normally run in release mode (otherwise it's really slow), so the first step is to open up libs/math/test/Jamfile.v2 and change:  
  
```  
   [ run test_next.cpp pch ../../test/build//boost_unit_test_framework : : : release  ]  
```  
  
to  
  
```  
   [ run test_next.cpp pch ../../test/build//boost_unit_test_framework : : : ]  
```  
  
Then cd into libs/math/test and run:  
  
```  
UBSAN_OPTIONS=print_stacktrace=1 ../../../b2 toolset=gcc-7 cxxstd=03 cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined cxxflags=-fno-sanitize-recover=undefined linkflags=-fsanitize=undefined linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold variant=debug -q -a test_next  
```  
  
and you should see the crash report - but not if you change the toolset to clang.  
  
If I debug the executable, I get to cpp_bin_float.hpp:1519:  
  
```  
   switch(arg.exponent())  
```  
  
The exponent inside arg has value -2147483312 (which is correct), but then if I step through cpp_bin_float::exponent() which is a trivial getter function:  
  
```  
   const exponent_type& exponent()const { return m_exponent; }  
```  
  
It dies, complaining that -2147483312 -2147483312 is out of range ???  
  
All this happens in program startup code, so I never get to the error in the report - which is also not triggered with clang's usan.  
  
BTW the values in the original report look strange to me - they require  the exponent to be INT_MIN which should never happen.  
  
All this is with current develop BTW.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-07-29 09:18:47 UTC  
> Url: https://github.com/boostorg/math/issues/135#issuecomment-408663358  

Update: the issue I was seeing seems to be specific to gcc8 and switch statements - if I replace the switch with the equivalent `if` statement then I get past that statement just fine, and then it dies the next time it encounters a switch on the exponent value - which are pervasive throughout the source and should be perfectly well defined.  
  
BTW I'm completely unable to reproduce the original issue - gcc-7 passes that test just fine using the command line from the OP.  Also checked with multiprecision 1.66, 1.67 and master/develop.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-07-29 17:31:59 UTC  
> Url: https://github.com/boostorg/math/issues/135#issuecomment-408692902  

>BTW I'm completely unable to reproduce the original issue - gcc-7 passes that test just fine using the command line from the OP. Also checked with multiprecision 1.66, 1.67 and master/develop.  
  
Never mind, reproduced, fix in the works.  
  
Still have issues with the gcc-8 failure though.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-07-30 11:49:32 UTC  
> Url: https://github.com/boostorg/math/issues/135#issuecomment-408836823  

This should now be fixed in develop.  There are still a few failures from the tests which use serialization though in both Math and Multiprecision libs.
