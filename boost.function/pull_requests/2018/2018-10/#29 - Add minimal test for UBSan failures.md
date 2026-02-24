# #29 Add minimal test for UBSan failures [Closed]

> Username: mloskot  
> Created at: 2018-10-22 17:51:14 UTC  
> Updated at: 2018-10-25 12:55:24 UTC  
> Closed at: 2018-10-25 12:45:50 UTC  
> Url: https://github.com/boostorg/function/pull/29  

_(this is an issue report but with mwe to reproduce it as per @pdimov suggestion on Slack)_  
  
Reproduced with clang 5.0 and 6.0 on Linux using Boost super-project at `master` as well as Function at `develop`:  
  
```shell  
cd libs/function/test/ubsan_undefined_bug  
b2 toolset=clang variant=ubsan_undefined  
```  
  
-----  
  
Full log:  
  
```shell  
$ b2 toolset=clang variant=ubsan_undefined  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
    - BOOST_COMP_GNUC >= 4.3.0 : no  
...patience...  
...found 2941 targets...  
...updating 61 targets...  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/system/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/error_code.o  
clang-linux.link.dll ../../../../bin.v2/libs/system/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/libboost_system.so.1.69.0  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/timer/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/auto_timers_construction.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/chrono/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/chrono.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/chrono/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/thread_clock.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/compiler_log_formatter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/debug.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/chrono/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/process_cpu_clocks.o  
clang-linux.link.dll ../../../../bin.v2/libs/chrono/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/libboost_chrono.so.1.69.0  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/timer/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/cpu_timer.o  
clang-linux.link.dll ../../../../bin.v2/libs/timer/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/libboost_timer.so.1.69.0  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/plain_report_formatter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/progress_monitor.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/decorator.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/execution_monitor.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test_framework_init_observer.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/results_collector.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/results_reporter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/unit_test_monitor.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test_tools.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/unit_test_main.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/unit_test_log.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test_tree.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/xml_log_formatter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/xml_report_formatter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/framework.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/junit_log_formatter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/unit_test_parameters.o  
clang-linux.link.dll ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/libboost_unit_test_framework.so.1.69.0  
clang-linux.link ../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test  
testing.capture-output ../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test.run  
====== BEGIN OUTPUT ======  
../../../../boost/function/function_template.hpp:768:14: runtime error: call to function (unknown) through pointer to incorrect function type 'void (*)(boost::detail::function::function_buffer &)'  
(/mnt/d/boost.wsl/bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test+0x42cf60): note: (unknown) defined here  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/mnt/d/boost.wsl/bin.v2/libs/chrono/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden:/mnt/d/boost.wsl/bin.v2/libs/system/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden:/mnt/d/boost.wsl/bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden:/mnt/d/boost.wsl/bin.v2/libs/timer/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test"   > "../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test.output"  
    echo EXIT STATUS: $status >> "../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test.output"  
    if test $status -eq 0 ; then  
        cp "../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test.output" "../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test.run...  
...failed updating 1 target...  
...skipped 1 target...  
...updated 59 targets...  
mloskot@bionic:/mnt/d/boost.wsl/libs/function/test/ubsan_undefined_bug$  
```  
  
Originally, the issue was discovered while running Boost.GIL tests.

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-10-22 22:37:02 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432015522  

The CI environment runs UBSAN already, so there should be no need for additional build changes.  Could you check the .travis.yml file and see if it is somehow insufficient?

---

## Review 2 [Commented]

> Username: jeking3  
> Created_at: 2018-10-22 22:37:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/function/pull/29#pullrequestreview-167138834  

📁 test/ubsan_undefined_bug/test.cpp

```diff
   1 |+ #define BOOST_TEST_MAIN
   2 |+ #include <boost/test/unit_test.hpp>
```

> Username: jeking3  
> Created_at: 2018-10-22 22:37:57 UTC  
> Url: https://github.com/boostorg/function/pull/29#discussion_r227162322  

This looks like a Boost.Test issue, not a Boost.Function issue?

> Username: mloskot  
> Created_at: 2018-10-23 08:26:57 UTC  
> Url: https://github.com/boostorg/function/pull/29#discussion_r227278804  

https://github.com/boostorg/function/pull/29#issuecomment-432022100


---

## Comment 3

> Username: swatanabe  
> Created_at: 2018-10-22 23:04:40 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432022100  

AMDG  
  
On 10/22/2018 04:37 PM, James E. King III wrote:  
>   
> This looks like a Boost.Test issue, not a Boost.Function issue?  
>   
  
  Maybe, maybe not.  Boost.Test uses Boost.Function,  
and the issue does show up in function.  It's a  
bit hard to tell what's going on with just the  
output shown.  I'd tend to suspect Function more  
than Test, given that all the type casting in Function  
is liable to result in exactly this sort of error  
if it's wrong.  Anyway, the test case is definitely  
not minimal if the problem is in fact in function.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-10-22 23:17:37 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432024878  

I suspect that `visibility=hidden` is the cause here.

---

## Comment 5

> Username: swatanabe  
> Created_at: 2018-10-22 23:28:32 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432027195  

AMDG  
  
On 10/22/2018 05:17 PM, Peter Dimov wrote:  
> I suspect that `visibility=hidden` is the cause here.  
>   
  
You're probably right.  The boost::function is  
created in the main executable, but is run  
inside boost_unit_test_framework.so.  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: Kojoley  
> Created_at: 2018-10-22 23:47:25 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432031096  

I saw this previously and yes it was because of `-fvisibility=hidden`. Making `operator()` visible solved problem for me.  
If you look on boost regression matrix there are bunch of these, boost::array f e.x https://www.boost.org/development/tests/develop/output/Marshall-UBSAN-boost-bin-v2-libs-array-test-array0-test-clang-darwin-ubsan-debug-cxxstd-2a-iso-visibility-hidden.html

---

## Comment 7

> Username: pdimov  
> Created_at: 2018-10-22 23:57:33 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432032958  

I just use `visibility=global` on UBSan builds.

---

## Comment 8

> Username: swatanabe  
> Created_at: 2018-10-23 00:02:39 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432033973  

AMDG  
  
On 10/22/2018 05:47 PM, Nikita Kniazev wrote:  
> I saw this previously and yes it was because of `-fvisibility=hidden`. Making `operator()` visible solved problem for me.  
  
It may solve the problem, it doesn't seem  
like a reliable solution to me.  
  
Assuming that I understand the problem correctly,  
the issue is that function_buffer is treated  
as a different type in different modules.  Making  
operator() visible just means that the definition  
of operator() which is consistent with the creation  
of the function, /might/ be the one chosen.  
  
> If you look on boost regression matrix there are bunch of these, boost::array f e.x https://www.boost.org/development/tests/develop/output/Marshall-UBSAN-boost-bin-v2-libs-array-test-array0-test-clang-darwin-ubsan-debug-cxxstd-2a-iso-visibility-hidden.html  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 9

> Username: Kojoley  
> Created_at: 2018-10-23 00:03:27 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432034145  

@pdimov I remember. Maybe you want to debate on the issue with GCC and Clang devs? :smirk:

---

## Comment 10

> Username: pdimov  
> Created_at: 2018-10-23 00:05:42 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432034612  

Can't do much when the errors come from the stdlib. :-)

---

## Comment 11

> Username: Kojoley  
> Created_at: 2018-10-23 00:08:43 UTC  
> Updated_at: 2018-10-23 00:09:38 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432035236  

Is not the problem is in the `reinterpret_cast` because casting between from different function pointer types is UB (and it is was `boost::function` exactly does).

---

## Comment 12

> Username: Kojoley  
> Created_at: 2018-10-23 00:11:13 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432035712  

> Can't do much when the errors come from the stdlib. :-)  
  
You can reproduce the same with `std::function`?

---

## Comment 13

> Username: swatanabe  
> Created_at: 2018-10-23 00:16:17 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432036720  

AMDG  
  
On 10/22/2018 06:08 PM, Nikita Kniazev wrote:  
> @swatanabe Is not the problem is in the `reinterp_cast` because casting between from different type of function pointers is UB (and it is was `boost::function` exactly does).  
>   
  
The reinterpret_cast is not undefined behavior by  
itself.  It's perfectly legal as long as you cast  
the pointer back to the correct type before  
calling it.  
  
In Christ,  
Steven Watanabe

---

## Comment 14

> Username: Kojoley  
> Created_at: 2018-10-23 00:20:19 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432037443  

Also is not https://github.com/boostorg/function/blob/b07621f2ee69996d491ea8535dae05e0ed6e7555/include/boost/function/function_template.hpp#L906-L908 breaks strict aliasing too, should not it be           `std::memcpy(&this->functor, &f.functor, sizeof(this->functor));`

---

## Comment 15

> Username: pdimov  
> Created_at: 2018-10-23 00:28:23 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432038855  

No, `data` is a char array, can't break strict aliasing with that.

---

## Comment 16

> Username: Kojoley  
> Created_at: 2018-10-23 00:32:51 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432039732  

Duuno why I wrote about strict aliasing. Reading other member of union different that it was initialized/last assigned is UB, is not it?

---

## Comment 17

> Username: pdimov  
> Created_at: 2018-10-23 00:41:56 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432041445  

It is, in principle, yes. I'm not sure about the exact rules though, char arrays may be exempt. But if this were diagnosed by UBSan, all `function` tests would fail. If you need an .so to trigger the failure, visibility is a more likely culprit.  
  
Let's see what Travis has to say about #30.

---

## Comment 18

> Username: swatanabe  
> Created_at: 2018-10-23 00:57:01 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432044474  

AMDG  
  
On 10/22/2018 06:41 PM, Peter Dimov wrote:  
> It is, in principle, yes. I'm not sure about the exact rules though, char arrays may be exempt. <snip>  
>   
  
In this case, it doesn't matter, because the pointer  
is converted to void* anyway.  (void*)&this->functor  
is guaranteed to be equal to (void*)this->data.  
  
In Christ,  
Steven Watanabe

---

## Comment 19

> Username: Kojoley  
> Created_at: 2018-10-23 01:15:45 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432048409  

For some reason GCC does not catch this problem, only Clang.

---

## Comment 20

> Username: mloskot  
> Created_at: 2018-10-23 07:00:21 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432119397  

@jeking3 I'm not requesting to add changes submitted via this PR. I think the first sentence in bracket emphasised it. I'm submitting the issue via PR for easy reproduction, as code presentation. I believe other comments that followed helped to clarify that.

---

## Comment 21

> Username: mloskot  
> Created_at: 2018-10-23 08:55:02 UTC  
> Updated_at: 2018-10-23 08:55:14 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432158332  

@pdimov https://github.com/boostorg/function/pull/29#issuecomment-432032958  
> I just use visibility=global on UBSan builds.  
  
I've tried this, but apparently Boost.Test overrides to `hidden` as it generates `/visibility-hidden/`, doesn't it?  
  
```  
$ b2 toolset=clang variant=ubsan_undefined visibility=global  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
    - BOOST_COMP_GNUC >= 4.3.0 : no  
...patience...  
...found 2941 targets...  
...updating 61 targets...  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/system/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/error_code.o  
clang-linux.link.dll ../../../../bin.v2/libs/system/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/libboost_system.so.1.69.0  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/timer/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/auto_timers_construction.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/chrono/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/chrono.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/chrono/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/thread_clock.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-global/test.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/compiler_log_formatter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/debug.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/chrono/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/process_cpu_clocks.o  
clang-linux.link.dll ../../../../bin.v2/libs/chrono/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/libboost_chrono.so.1.69.0  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/timer/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/cpu_timer.o  
clang-linux.link.dll ../../../../bin.v2/libs/timer/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/libboost_timer.so.1.69.0  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/plain_report_formatter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/progress_monitor.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/decorator.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/execution_monitor.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test_framework_init_observer.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/results_collector.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/results_reporter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/unit_test_monitor.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test_tools.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/unit_test_main.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/unit_test_log.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/test_tree.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/framework.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/xml_log_formatter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/xml_report_formatter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/junit_log_formatter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/unit_test_parameters.o  
clang-linux.link.dll ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-hidden/libboost_unit_test_framework.so.1.69.0  
clang-linux.link ../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-global/test  
testing.capture-output ../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-global/test.run  
====== BEGIN OUTPUT ======  
../../../../boost/function/function_template.hpp:768:14: runtime error: call to function (unknown) through pointer to incorrect function type 'void (*)(boost::detail::function::function_buffer &)'  
(/mnt/d/boost.wsl/bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-global/test+0x42cfa0): note: (unknown) defined here  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```

---

## Comment 22

> Username: pdimov  
> Created_at: 2018-10-23 09:16:53 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432166445  

Is this master or develop?

---

## Comment 23

> Username: mloskot  
> Created_at: 2018-10-23 09:29:39 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432171183  

Boost.Function is `ml/ubsan_bug` based on `develop`.  
  
Then, I've tried both:  
* Boost.Test at https://github.com/boostorg/test/commit/f24f8fd38836597228c63d96e9ff80f8b3cc5ba0, that is the super-project's `master` :-)  
* Boost.Test `develop`  
  
I have not tried the super-project at `develop` though. Is this what you are suggesting to do?

---

## Comment 24

> Username: pdimov  
> Created_at: 2018-10-23 09:34:54 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432173200  

Seems like https://github.com/boostorg/build/commit/d8245f6686947f2f862e4f6eca2de788909eca6b hasn't been merged to master yet, so `visibility=global` doesn't propagate to the library if you're using Boost.Build from master.

---

## Comment 25

> Username: mloskot  
> Created_at: 2018-10-23 10:22:26 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432189323  

Bingo!  
  
Total clean up of my working copy and switch to super-project `develop` branch (Function at `ml/ubsan-bug` based on `develop`):  
  
```  
git clean -xfd  
git submodule foreach --recursive git clean -xfd  
git reset --hard  
git submodule foreach --recursive git reset --hard  
git checkout develop  
git pull  
git submodule update --init --recursive --jobs 8  
```  
  
followed by `./bootstrap.sh && ./b2 headers` solved the problem indeed:  
  
```  
$ b2 -j6 toolset=clang variant=ubsan_undefined visibility=global  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
    - BOOST_COMP_GNUC >= 4.3.0 : no  
...patience...  
...found 2925 targets...  
...updating 61 targets...  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/system/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/error_code.o  
clang-linux.link.dll ../../../../bin.v2/libs/system/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/libboost_system.so.1.69.0  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/timer/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/auto_timers_construction.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/chrono/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/chrono.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/timer/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/cpu_timer.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/chrono/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/thread_clock.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-global/test.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/compiler_log_formatter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/chrono/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/process_cpu_clocks.o  
clang-linux.link.dll ../../../../bin.v2/libs/chrono/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/libboost_chrono.so.1.69.0  
clang-linux.link.dll ../../../../bin.v2/libs/timer/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/libboost_timer.so.1.69.0  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/debug.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/decorator.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/plain_report_formatter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/execution_monitor.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/progress_monitor.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/test_framework_init_observer.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/results_collector.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/results_reporter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/unit_test_monitor.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/test_tools.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/unit_test_main.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/unit_test_log.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/test_tree.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/xml_report_formatter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/xml_log_formatter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/framework.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/junit_log_formatter.o  
clang-linux.compile.c++.without-pth ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/unit_test_parameters.o  
clang-linux.link.dll ../../../../bin.v2/libs/test/build/clang-linux-6.0.0/ubsan_undefined/visibility-global/libboost_unit_test_framework.so.1.69.0  
clang-linux.link ../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-global/test  
testing.capture-output ../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-global/test.run  
**passed** ../../../../bin.v2/libs/function/test/ubsan_undefined_bug/test.test/clang-linux-6.0.0/ubsan_undefined/visibility-global/test.test  
...updated 61 targets...  
```  
  
It looks like one of those (common?) situations when "test your library against super-project `master`" best practice is not the best, doesn't it?

---

## Comment 26

> Username: codecov[bot]  
> Created_at: 2018-10-23 12:54:50 UTC  
> Updated_at: 2018-10-23 13:44:10 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432234670  

# [Codecov](https://codecov.io/gh/boostorg/function/pull/29?src=pr&el=h1) Report  
> Merging [#29](https://codecov.io/gh/boostorg/function/pull/29?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/function/commit/453860ff9cfac59c53bd3936e95324b2e6318b97?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/function/pull/29/graphs/tree.svg?width=650&token=beMMliwLU7&height=150&src=pr)](https://codecov.io/gh/boostorg/function/pull/29?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #29   +/-   ##  
========================================  
  Coverage    54.24%   54.24%             
========================================  
  Files            4        4             
  Lines          389      389             
  Branches        98       98             
========================================  
  Hits           211      211             
  Misses          85       85             
  Partials        93       93  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/function/pull/29?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/function/function\_template.hpp](https://codecov.io/gh/boostorg/function/pull/29/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mdW5jdGlvbi9mdW5jdGlvbl90ZW1wbGF0ZS5ocHA=) | `64% <0%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/function/pull/29?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/function/pull/29?src=pr&el=footer). Last update [453860f...d6b017c](https://codecov.io/gh/boostorg/function/pull/29?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 27

> Username: mloskot  
> Created_at: 2018-10-23 13:43:39 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-432252291  

Does this qualify for closure now? Am I supposed to close it?

---

## Comment 28

> Username: jeking3  
> Created_at: 2018-10-25 12:09:11 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-433025502  

It looks like your issue was environmental, so yes you can close it.  
https://github.com/boostorg/gil/pull/161 looks dangerous long-term.

---

## Comment 29

> Username: Kojoley  
> Created_at: 2018-10-25 12:11:32 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-433026177  

@jeking3 you can close this one in favor of #31.

---

## Comment 30

> Username: mloskot  
> Created_at: 2018-10-25 12:45:50 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-433036355  

@jeking3   
> boostorg/gil#161 looks dangerous long-term.  
  
It says "we are switching temporarily".  
  
However, although I've been following [Robert Ramey's maintenance guidelines](https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#1-clone-boost-super-project) to test my library against the super-project `master`, I have also heard antithetic opinions.

---

## Comment 31

> Username: Kojoley  
> Created_at: 2018-10-25 12:53:44 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-433038824  

> to test my library against the super-project `master`  
  
This may save you some time, but it will make you lag behind the actual version, because a lot of libraries rarely merge to `master`, so in a few days before gates are closing for the beta/release you will have a headache.

---

## Comment 32

> Username: mloskot  
> Created_at: 2018-10-25 12:55:24 UTC  
> Url: https://github.com/boostorg/function/pull/29#issuecomment-433039351  

@Kojoley Yes, indeed, I've experienced issues from the recommended testing workflow

---
