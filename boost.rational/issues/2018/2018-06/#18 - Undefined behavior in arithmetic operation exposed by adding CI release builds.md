# #18 - Undefined behavior in arithmetic operation exposed by adding CI release builds [Closed]

> Username: jeking3  
> Created at: 2018-06-06 18:51:09 UTC  
> Updated at: 2018-06-14 15:02:30 UTC  
> Closed at: 2018-06-14 12:13:25 UTC  
> Url: https://github.com/boostorg/rational/issues/18  

I needed a place to store these temporarily.  When adding CI to the repository (https://github.com/jeking3/rational/pull/1) it exposed a number of failures building.  
  
https://travis-ci.org/jeking3/rational/jobs/384444075  
```  
testing.capture-output ../../bin.v2/libs/rational/test/rational_test.test/gcc-4.8/release/cxxstd-03-iso/rational_test.run  
also  
testing.capture-output ../../bin.v2/libs/rational/test/rational_test.test/clang-linux-5.0.0/release/cxxstd-11-iso/rational_test.run  
  
It appears release builds are broken.  
  
====== BEGIN OUTPUT ======  
Running tests for boost::rational<long>  
Implementation issue: the minimal size for a rational  
is twice the size of the underlying integer type.  
Checking to see if space is being wasted.  
	sizeof(long) == 8  
	sizeof(boost::rational<long>) == 16  
Implementation has minimal size  
Running 83 test cases...  
test/rational_test.cpp(943): error: in "rational_arithmetic_suite/conversions": exception std::domain_error expected but not raised  
test/rational_test.cpp(950): error: in "rational_arithmetic_suite/conversions": exception std::domain_error expected but not raised  
test/rational_test.cpp(966): error: in "rational_arithmetic_suite/conversions": exception std::domain_error expected but not raised  
test/rational_test.cpp(972): error: in "rational_arithmetic_suite/conversions": exception std::domain_error expected but not raised  
test/rational_test.cpp(1239): error: in "rational_extras_suite/rational_input_failing_test<int>": exception boost::bad_rational expected but not raised  
test/rational_test.cpp(1240): error: in "rational_extras_suite/rational_input_failing_test<int>": check iss.fail() && !iss.bad() has failed  
test/rational_test.cpp(1239): error: in "rational_extras_suite/rational_input_failing_test<long>": exception boost::bad_rational expected but not raised  
test/rational_test.cpp(1240): error: in "rational_extras_suite/rational_input_failing_test<long>": check iss.fail() && !iss.bad() has failed  
test/rational_test.cpp(1239): error: in "rational_extras_suite/rational_input_failing_test<(anonymous namespace)__MyInt>": exception boost::bad_rational expected but not raised  
test/rational_test.cpp(1240): error: in "rational_extras_suite/rational_input_failing_test<(anonymous namespace)__MyInt>": check iss.fail() && !iss.bad() has failed  
test/rational_test.cpp(1600): error: in "bug_patch_request_suite/ticket_9067_test": exception boost::bad_rational expected but not raised  
test/rational_test.cpp(1601): error: in "bug_patch_request_suite/ticket_9067_test": check a.denominator() > 0 has failed  
test/rational_test.cpp(1603): error: in "bug_patch_request_suite/ticket_9067_test": check a.numerator() == -3 has failed [-2147483647 != -3]  
test/rational_test.cpp(1604): error: in "bug_patch_request_suite/ticket_9067_test": check a.denominator() == 4 has failed [-2147483648 != 4]  
*** 14 failures are detected in the test module "Master Test Suite"  
EXIT STATUS: 201  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2018-06-06 18:56:08 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-395176717  

gdb session  
```  
(gdb) b rational_test.cpp:943  
Breakpoint 1 at 0x15f21: file test/rational_test.cpp, line 943.  
(gdb) r  
Starting program: /boost/bin.v2/libs/rational/test/rational_test.test/gcc-7.3/debug/rational_test  
warning: Error disabling address space randomization: Operation not permitted  
Running tests for boost::rational<long>  
  
Implementation issue: the minimal size for a rational  
is twice the size of the underlying integer type.  
  
Checking to see if space is being wasted.  
        sizeof(long) == 8  
        sizeof(boost::rational<long>) == 16  
  
Implementation has minimal size  
  
Running 83 test cases...  
  
Breakpoint 1, rational_arithmetic_suite::conversions::test_method (this=0x7fff5be55b3e) at test/rational_test.cpp:943  
943        BOOST_CHECK_THROW(signed_rat(1, signed_min).denominator(), std::domain_error);  
(gdb) catch throw  
Catchpoint 2 (throw)  
(gdb) c  
Continuing.  
  
Catchpoint 2 (exception thrown), 0x00007f6790e0ffb1 in __cxa_throw () from /usr/lib/x86_64-linux-gnu/libstdc++.so.6  
(gdb) bt  
#0  0x00007f6790e0ffb1 in __cxa_throw () from /usr/lib/x86_64-linux-gnu/libstdc++.so.6  
#1  0x00005633287055cd in boost::throw_exception<boost::exception_detail::error_info_injector<boost::bad_rational> > (e=...)  
    at ../../boost/throw_exception.hpp:71  
#2  0x0000563328703480 in boost::exception_detail::throw_exception_<boost::bad_rational> (x=...,  
    current_function=0x56332880b000 <boost::rational<int>::normalize()::__PRETTY_FUNCTION__> "void boost::rational<I>::normalize() [with IntType = int]", file=0x563328806536 "../../boost/rational.hpp", line=870) at ../../boost/throw_exception.hpp:88  
#3  0x0000563328704539 in boost::rational<int>::normalize (this=0x7fff5be559a0) at ../../boost/rational.hpp:870  
#4  0x00005633286ff597 in boost::rational<int>::rational<int, int>(int const&, int const&, boost::enable_if_c<boost::rational_detail::is_compatible_integer<int, int>::value&&boost::rational_detail::is_compatible_integer<int, int>::value, void>::type const*) (this=0x7fff5be559a0,  
    n=@0x7fff5be55ac0: 1, d=@0x7fff5be557b4: -2147483648) at ../../boost/rational.hpp:163  
#5  0x00005633286acf8a in rational_arithmetic_suite::conversions::test_method (this=0x7fff5be55b3e) at test/rational_test.cpp:943  
#6  0x00005633286ac5f8 in rational_arithmetic_suite::conversions_invoker () at test/rational_test.cpp:928  
#7  0x0000563328707229 in boost::detail::function::void_function_invoker0<void (*)(), void>::invoke (function_ptr=...)  
    at ../../boost/function/function_template.hpp:118  
#8  0x00005633287e1eb5 in boost::function0<void>::operator() (this=0x56332a8e2008) at ../../../boost/function/function_template.hpp:768  
#9  0x0000563328802c03 in boost::detail::forward::operator() (this=0x7fff5be56a88) at ../../../boost/test/impl/execution_monitor.ipp:1310  
#10 0x0000563328803936 in boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke (function_obj_ptr=...)  
    at ../../../boost/function/function_template.hpp:138  
#11 0x0000563328803713 in boost::function0<int>::operator() (this=0x7fff5be56a80) at ../../../boost/function/function_template.hpp:769  
#12 0x0000563328802f3b in boost::detail::do_invoke<boost::shared_ptr<boost::detail::translator_holder_base>, boost::function<int ()> >(boost::shared_ptr<boost::detail::translator_holder_base> const&, boost::function<int ()> const&) (tr=..., F=...)  
    at ../../../boost/test/impl/execution_monitor.ipp:285  
#13 0x000056332880199e in boost::execution_monitor::catch_signals(boost::function<int ()> const&) (  
    this=0x563328a9d0a0 <boost::unit_test::singleton<boost::unit_test::unit_test_monitor_t>::instance()::the_inst>, F=...)  
    at ../../../boost/test/impl/execution_monitor.ipp:874  
#14 0x0000563328801b22 in boost::execution_monitor::execute(boost::function<int ()> const&) (  
    this=0x563328a9d0a0 <boost::unit_test::singleton<boost::unit_test::unit_test_monitor_t>::instance()::the_inst>, F=...)  
    at ../../../boost/test/impl/execution_monitor.ipp:1213  
#15 0x000056332880279c in boost::execution_monitor::vexecute(boost::function<void ()> const&) (  
    this=0x563328a9d0a0 <boost::unit_test::singleton<boost::unit_test::unit_test_monitor_t>::instance()::the_inst>, F=...)  
    at ../../../boost/test/impl/execution_monitor.ipp:1319  
#16 0x00005633287fb7f4 in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned int) (  
    this=0x563328a9d0a0 <boost::unit_test::singleton<boost::unit_test::unit_test_monitor_t>::instance()::the_inst>, func=..., timeout=0)  
    at ../../../boost/test/impl/unit_test_monitor.ipp:46  
#17 0x00005633287e0d70 in boost::unit_test::framework::state::execute_test_tree (  
    this=0x563328a9cf20 <boost::unit_test::framework::impl::(anonymous namespace)::s_frk_state()::the_inst>, tu_id=65595, timeout=0,  
    p_random_generator=0x7fff5be56e52) at ../../../boost/test/impl/framework.ipp:786  
#18 0x00005633287e07fa in boost::unit_test::framework::state::execute_test_tree (  
    this=0x563328a9cf20 <boost::unit_test::framework::impl::(anonymous namespace)::s_frk_state()::the_inst>, tu_id=4, timeout=0,  
    p_random_generator=0x7fff5be57102) at ../../../boost/test/impl/framework.ipp:734  
#19 0x00005633287e07fa in boost::unit_test::framework::state::execute_test_tree (  
    this=0x563328a9cf20 <boost::unit_test::framework::impl::(anonymous namespace)::s_frk_state()::the_inst>, tu_id=1, timeout=0,  
    p_random_generator=0x0) at ../../../boost/test/impl/framework.ipp:734  
#20 0x00005633287daee5 in boost::unit_test::framework::run (id=1, continue_test=true) at ../../../boost/test/impl/framework.ipp:1619  
#21 0x0000563328785950 in boost::unit_test::unit_test_main (init_func=0x5633286ab6fa <init_unit_test_suite(int, char**)>, argc=1,  
    argv=0x7fff5be57718) at ../../../boost/test/impl/unit_test_main.ipp:231  
#22 0x0000563328785c03 in main (argc=1, argv=0x7fff5be57718) at ../../../boost/test/impl/unit_test_main.ipp:284  
(gdb) f 3  
#3  0x0000563328704539 in boost::rational<int>::normalize (this=0x7fff5be559a0) at ../../boost/rational.hpp:870  
870            BOOST_THROW_EXCEPTION(bad_rational("bad rational: non-zero singular denominator"));  
(gdb) f 4  
#4  0x00005633286ff597 in boost::rational<int>::rational<int, int>(int const&, int const&, boost::enable_if_c<boost::rational_detail::is_compatible_integer<int, int>::value&&boost::rational_detail::is_compatible_integer<int, int>::value, void>::type const*) (this=0x7fff5be559a0,  
    n=@0x7fff5be55ac0: 1, d=@0x7fff5be557b4: -2147483648) at ../../boost/rational.hpp:163  
163            normalize();  
(gdb) f 3  
#3  0x0000563328704539 in boost::rational<int>::normalize (this=0x7fff5be559a0) at ../../boost/rational.hpp:870  
870            BOOST_THROW_EXCEPTION(bad_rational("bad rational: non-zero singular denominator"));  
(gdb) l  
865         // ...But acknowledge that the previous step doesn't always work.  
866         // (Nominally, this should be done before the mutating steps, but this  
867         // member function is only called during the constructor, so we never have  
868         // to worry about zombie objects.)  
869         if (den < zero)  
870            BOOST_THROW_EXCEPTION(bad_rational("bad rational: non-zero singular denominator"));  
871  
872         BOOST_ASSERT( this->test_invariant() );  
873     }  
874  
(gdb) p den  
$1 = -2147483648  
```

---

## Comment 2

> Username: jeking3  
> Created at: 2018-06-12 19:23:59 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-396704615  

Additional information about failure environment(s):  
  
Ubuntu Bionic  
  
gcc  
  unit test succeeds: -O0, -O1  
  unit test fails: -O2, -O3  
boost@17b66a9efac4:/boost/libs/rational/test$ g++ --version  
g++ (Ubuntu 7.3.0-16ubuntu3) 7.3.0  
Copyright (C) 2017 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
clang  
  unit test succeeds: -O0  
  unit test fails: -O1, -O2, -O3  
boost@17b66a9efac4:/boost/libs/rational/test$ clang++-6.0 --version  
clang version 6.0.0-1ubuntu2 (tags/RELEASE_600/final)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
  
To reproduce, try:  
  
boost@17b66a9efac4:/boost/libs/rational/test$ ../../../b2 toolset=clang variant=release rational_test  
  
I am using the ubuntu bionic docker container to do the build in.  See https://github.com/boostorg/boost/pull/184

---

## Comment 3

> Username: mclow  
> Created at: 2018-06-12 19:45:41 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-396710403  

My first guess given that different optimization levels change the result is undefined behavior, but the clang-ubsan tests all pass.  
  
I'll look some more (in a non-docker environment).

---

## Comment 4

> Username: mclow  
> Created at: 2018-06-12 21:14:41 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-396736272  

On my Mac; with Apple clang v. 9.1.0, the rational tests pass at `-O0` and `-02`. [ More data points ]

---

## Comment 5

> Username: jeking3  
> Created at: 2018-06-13 14:01:10 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-396948087  

I decided to build with variant=debug cxxflags=-O1 (or -O2 or -O3) and expand the compilers.  
Once I switched to variant=debug this unlocked the assertion checking in the code.  
  
| compiler  |  O1  |  O2  |  
| :-------- | ---- | ---- |  
| g++-4.8.5 | pass | fail |   
| g++-5.5.0 | pass | fail |   
| g++-6.4.0 | pass | fail |   
| g++-7.3.0 | pass | fail |   
| g++-8.0.1 | fail | fail |   
  
In this mode I am seeing a different error now.  Each "fail" above is showing the following:  
  
```  
Running 83 test cases...  
unknown location(0): fatal error: in "rational_arithmetic_suite/conversions": signal: SIGABRT (application abort requested)  
rational_test.cpp(943): last checkpoint  
rational_test: ../../../boost/rational.hpp:872: void boost::rational<I>::normalize() [with IntType = int]: Assertion `this->test_invariant()' failed.  
unknown location(0): fatal error: in "rational_extras_suite/rational_input_failing_test<int>": signal: SIGABRT (application abort requested)  
rational_test.cpp(1239): last checkpoint  
rational_test: ../../../boost/rational.hpp:872: void boost::rational<I>::normalize() [with IntType = long int]: Assertion `this->test_invariant()' failed.  
unknown location(0): fatal error: in "rational_extras_suite/rational_input_failing_test<long>": signal: SIGABRT (application abort requested)  
rational_test.cpp(1239): last checkpoint  
rational_test: ../../../boost/rational.hpp:872: void boost::rational<I>::normalize() [with IntType = int]: Assertion `this->test_invariant()' failed.  
unknown location(0): fatal error: in "bug_patch_request_suite/ticket_9067_test": signal: SIGABRT (application abort requested)  
rational_test.cpp(1600): last checkpoint  
  
*** 4 failures are detected in the test module "Master Test Suite"  
```  
  
This is likely a good clue as to what's going on.

---

## Comment 6

> Username: jeking3  
> Created at: 2018-06-13 14:30:41 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-396958526  

Annotated test_invariant to see the values.  Here's with -O2:  
```  
test_invariant: num = -1073741821, den = 6  
test_invariant: num = -1073741827, den = 6  
test_invariant: num = -3, den = 4  
test_invariant: num = -3, den = 4  
test_invariant: num = -2147483647, den = -2147483648  
rational_test: ../../../boost/rational.hpp:877: void boost::rational<I>::normalize() [with IntType = int]: Assertion `this->test_invariant()' failed.  
unknown location(0): fatal error: in "bug_patch_request_suite/ticket_9067_test": signal: SIGABRT (application abort requested)  
rational_test.cpp(1600): last checkpoint  
```  
Here's with -O0:  
```  
test_invariant: num = -1073741821, den = 6  
test_invariant: num = -1073741827, den = 6  
test_invariant: num = -3, den = 4  
test_invariant: num = -3, den = 4  
  
*** No errors detected  
```  
So it never gets to test_invariant.  Interesting.

---

## Comment 7

> Username: jeking3  
> Created at: 2018-06-13 14:50:48 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-396965676  

Annotated normalize with -O0:  
  
```  
normalize (before): num = 6, den = -8  
normalize (after ): num = -3, den = 4, g = 2  
test_invariant: num = -3, den = 4  
normalize (before): num = -9, den = 12  
normalize (after ): num = -3, den = 4, g = 3  
test_invariant: num = -3, den = 4  
normalize (before): num = 2147483647, den = -2147483648  
normalize (after ): num = -2147483647, den = -2147483648, g = 1  
  
*** No errors detected  
```  
  
  
Here's with -O2:  
```  
normalize (before): num = 6, den = -8  
normalize (after ): num = -3, den = 4, g = 2  
test_invariant: num = -3, den = 4  
normalize (before): num = -9, den = 12  
normalize (after ): num = -3, den = 4, g = 3  
test_invariant: num = -3, den = 4  
normalize (before): num = 2147483647, den = -2147483648  
normalize (after ): num = -2147483647, den = -2147483648, g = 1  
  
*** No errors detected  
```  
  
The addition of the output changed the behavior of the program... now it works?

---

## Comment 8

> Username: mclow  
> Created at: 2018-06-13 14:58:39 UTC  
> Updated at: 2018-06-13 14:59:11 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-396968594  

What happens when you remove this line?  
  
      BOOST_CHECK_THROW( a.assign(-(INT_MIN + 1), INT_MIN), boost::bad_rational );  
  
That's in the ticket_9067_test test, btw.

---

## Comment 9

> Username: jeking3  
> Created at: 2018-06-13 14:59:26 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-396968912  

So I removed all the annotation crap and modified normalize() as follows:  
```  
    // Ensure that the denominator is positive  
    if (den < zero) {  
        num = -num;  
        den = -den;  
    }  
+  
+   asm volatile("": : :"memory");  
  
    // ...But acknowledge that the previous step doesn't always work.  
    // (Nominally, this should be done before the mutating steps, but this  
    // member function is only called during the constructor, so we never have  
    // to worry about zombie objects.)  
    if (den < zero)  
       BOOST_THROW_EXCEPTION(bad_rational("bad rational: non-zero singular denominator"));  
```  
  
Now everything passes.  I'm reluctant to say this is a generic compiler bug in both gcc and clang, but when I was trying to step through with the debugger in -O2 mode I really did see some strange jumping around behavior that I could not explain.

---

## Comment 10

> Username: jeking3  
> Created at: 2018-06-13 15:23:55 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-396978217  

Is there a Boost macro that's cross platform and achieves the same thing?  I admit I don't fully understand WHY it is happening but there appears to be a way to prevent the incorrect behavior.

---

## Comment 11

> Username: swatanabe  
> Created at: 2018-06-13 15:37:45 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-396983195  

AMDG  
  
On 06/13/2018 09:24 AM, James E. King III wrote:  
> Is there a Boost macro that's cross platform and achieves the same thing?  I admit I don't fully understand WHY it is happening but there appears to be a way to prevent the incorrect behavior.  
>   
  
  I really don't think this is an acceptable solution,  
unless we're reasonably confident that it is in fact  
a compiler bug.  
  
In Christ,  
Steven Watanabe

---

## Comment 12

> Username: jeking3  
> Created at: 2018-06-13 15:39:27 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-396983916  

Oh, I agree completely, I am just trying to figure out what's going on.  
  
I found this, and adding -fwrapv to the build does seem to fix the issue as well:  
  
https://stackoverflow.com/questions/8917233/why-does-int-min-int-min-in-a-signed-twos-complement-representation  
  
Perhaps this is a test asking for something unrealistic, based on undefined behavior.  Clearly different depending on the optimization level.

---

## Comment 13

> Username: jeking3  
> Created at: 2018-06-13 15:40:51 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-396984407  

-fwrapv also fixes clang-6.0 builds.

---

## Comment 14

> Username: swatanabe  
> Created at: 2018-06-13 15:47:48 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-396986778  

AMDG  
  
On 06/13/2018 08:59 AM, James E. King III wrote:  
> So I removed all the annotation crap and modified normalize() as follows:  
> ```  
>     // Ensure that the denominator is positive  
>     if (den < zero) {  
>         num = -num;  
>         den = -den;  
>     }  
> +  
> +   asm volatile("": : :"memory");  
>   
>     // ...But acknowledge that the previous step doesn't always work.  
  
  Actually, now that I think about it, the  
problem is pretty obvious.  If the previous  
step fails, it has undefined behavior, and  
attempting to fix it up after the fact is  
not guaranteed to work (i.e. the compiler can  
legitimately remove this second test).  We  
should test (den < -(numeric_limits<T>::max)())  
before running `den = -den`.  
  
>     // (Nominally, this should be done before the mutating steps, but this  
>     // member function is only called during the constructor, so we never have  
>     // to worry about zombie objects.)  
>     if (den < zero)  
>        BOOST_THROW_EXCEPTION(bad_rational("bad rational: non-zero singular denominator"));  
> ```  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 15

> Username: jeking3  
> Created at: 2018-06-13 15:50:07 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-396987791  

Giving that a try now, without any other changes like -fwrapv

---

## Comment 16

> Username: jeking3  
> Created at: 2018-06-13 17:40:18 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-397023895  

This seems to have done the trick:  
```  
boost@4bae6fb63084:/boost/libs/rational/test$ git diff HEAD  
diff --git a/include/boost/rational.hpp b/include/boost/rational.hpp  
index 4fc06c9..8e71bb4 100644  
--- a/include/boost/rational.hpp  
+++ b/include/boost/rational.hpp  
@@ -856,19 +856,16 @@ void rational<IntType>::normalize()  
     num /= g;  
     den /= g;  
  
+    if (den == std::numeric_limits<IntType>::min()) {  
+        BOOST_THROW_EXCEPTION(bad_rational("bad rational: non-zero singular denominator"));  
+    }  
+  
     // Ensure that the denominator is positive  
     if (den < zero) {  
         num = -num;  
         den = -den;  
     }  
  
-    // ...But acknowledge that the previous step doesn't always work.  
-    // (Nominally, this should be done before the mutating steps, but this  
-    // member function is only called during the constructor, so we never have  
-    // to worry about zombie objects.)  
-    if (den < zero)  
-       BOOST_THROW_EXCEPTION(bad_rational("bad rational: non-zero singular denominator"));  
-  
     BOOST_ASSERT( this->test_invariant() );  
 }  
```

---

## Comment 17

> Username: swatanabe  
> Created at: 2018-06-13 18:19:16 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-397036433  

AMDG  
  
On 06/13/2018 11:40 AM, James E. King III wrote:  
> <snip>  
> +    if (den == std::numeric_limits<IntType>::min()) {  
> +        BOOST_THROW_EXCEPTION(bad_rational("bad rational: non-zero singular denominator"));  
> +    }  
> +  
  
  The test I posted was carefully formulated to  
be representation agnostic.  You're assuming  
two's complement.  (Although this is used on  
all current systems that I'm aware of, the  
standard makes no guarantees.)  
  
In Christ,  
Steven Watanabe

---

## Comment 18

> Username: jeking3  
> Created at: 2018-06-13 18:27:36 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-397039004  

I had a mild concern about making too many assumptions in the fix I posted in the PR, but I tried your suggestion but it caused more failures.  It's likely I applied it incorrectly though.  If you want to post a code snippet the way you would like to see it exactly I can run it locally and if that works I can update this PR.  Thanks.

---

## Comment 19

> Username: swatanabe  
> Created at: 2018-06-13 18:38:57 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-397042620  

AMDG  
  
On 06/13/2018 12:27 PM, James E. King III wrote:  
> I had a mild concern about making too many assumptions in the fix I posted in the PR, but I tried your suggestion but it caused more failures.  It's likely I applied it incorrectly though.  If you want to post a code snippet the way you would like to see it exactly I can run it locally and if that works I can update this PR.  Thanks.  
>   
  
Just a guess: my attempt failed with unsigned types, right?  
  
In Christ,  
Steven Watanabe

---

## Comment 20

> Username: swatanabe  
> Created at: 2018-06-13 19:09:22 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-397051956  

AMDG  
  
On 06/13/2018 12:27 PM, James E. King III wrote:  
> I had a mild concern about making too many assumptions in the fix I posted in the PR, but I tried your suggestion but it caused more failures.  It's likely I applied it incorrectly though.  If you want to post a code snippet the way you would like to see it exactly I can run it locally and if that works I can update this PR.  Thanks.  
>   
  
I tried this with gcc, clang, and msvc debug+release  
and it passed.  
  
```  
if (den < -(std::numeric_limits<IntType>::max)())  
    BOOST_THROW_EXCEPTION(bad_rational("bad rational: non-zero singular  
denominator"));  
```  
  
Note that this causes warnings for unsigned types, but  
is still correct, because it resolves to `den < 1` which  
is always false, since we already tested for 0.  It will  
also work correctly with implicit promotion to int for small  
unsigned types, because any positive value is greater  
than any negative value.  
  
In Christ,  
Steven Watanabe

---

## Comment 21

> Username: jeking3  
> Created at: 2018-06-14 02:46:47 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-397152136  

Your code works, but only if it is before the "// Ensure that the denominator is positive" block.  If it is after, gcc-7.3 still optimizes it out and it fails.  I'll push the working change.

---

## Comment 22

> Username: swatanabe  
> Created at: 2018-06-14 15:02:30 UTC  
> Url: https://github.com/boostorg/rational/issues/18#issuecomment-397327951  

AMDG  
  
On 06/13/2018 08:46 PM, James E. King III wrote:  
> Your code works, but only if it is before the "// Ensure that the denominator is positive" block.  
  
  Well, yeah.  Your patch also has to go in the  
same place.  That block is where the UB happens,  
so /any/ fix has to come before it.  
  
>  If it is after, gcc-7.3 still optimizes it out and it fails.  I'll push the working change.  
>   
  
In Christ,  
Steven Watanabe
