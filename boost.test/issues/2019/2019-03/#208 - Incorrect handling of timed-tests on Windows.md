# #208 - Incorrect handling of timed-tests on Windows [Closed]

> Username: spacelg  
> Created at: 2019-03-01 07:29:02 UTC  
> Updated at: 2019-10-05 10:30:20 UTC  
> Closed at: 2019-10-05 10:30:19 UTC  
> Url: https://github.com/boostorg/test/issues/208  

Environment:  
VS 2017 + Windows Server 2016  
  
Issue description:  
We build and run test for boost and we found one test failed to run after boost submodule test updated from fd945c to 412e3d [boostorg/test@b0d837c](https://github.com/boostorg/boost/commit/b0d837cb30b850746f16130664486ffdfff3e0a8) .Could you please take a look?  
  
Reproduce steps:  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\test\test  
  
ErrorMessage:  
testing.capture-output ..\out\x64rel\boost\bin.v2\libs\test\test\test-timeout-fail.test\msvc-14.1\release\threading-multi\test-timeout-fail.run  
        1 file(s) copied.  
...failed testing.capture-output ..\out\x64rel\boost\bin.v2\libs\test\test\test-timeout-fail.test\msvc-14.1\release\threading-multi\test-timeout-fail.run...  
...removing ..\out\x64rel\boost\bin.v2\libs\test\test\test-timeout-fail.test\msvc-14.1\release\threading-multi\test-timeout-fail.run  
testing.capture-output ..\out\x64rel\boost\bin.v2\libs\test\test\test-timeout-suite.test\msvc-14.1\release\threading-multi\test-timeout-suite.run  
  
[log_x64_test_104.log](https://github.com/boostorg/test/files/2918340/log_x64_test_104.log)

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-03-01 07:52:41 UTC  
> Url: https://github.com/boostorg/test/issues/208#issuecomment-468576022  

Hi,  
  
Thanks for the report. This is a timed test, the content looks like this:  
  
```  
BOOST_AUTO_TEST_CASE(test_fail, * utf::timeout(1))  
{  
    std::this_thread::sleep_for(std::chrono::milliseconds(2000));  
    BOOST_TEST(true);  
}  
```  
  
Basically, it should sleep for 2 secs, which is enough for exceeding the one second limit attached to the test. Any idea why the sleep might not work?

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-03-02 10:00:31 UTC  
> Url: https://github.com/boostorg/test/issues/208#issuecomment-468906050  

I am not able to reproduce the error on my end, but the failures are visible on the [test matrix](https://www.boost.org/development/tests/develop/developer/test.html).   
  
Would it be possible for you to change the content of that file `$boost/libs/test/test/writing-test-ts/test-timeout-fail.cpp` and increase the sleep to something bigger? or run the test and let me know how much execution time is reported?  
  
For running the single test:  
  
```  
cd $boost/libs/test/test  
  
# this will run the test via b2  
# before the run, b2 outputs something like  
#.   XXX.link ../../../bin.v2/libs/test/test/test-timeout-fail.test/XXXX/release/threading-multi/visibility-hidden/test-timeout-fail  
# this contains the path of the generated binary that is needed in the second step  
../../../b2 --abbreviate-paths address-model=64 toolset=msvc-14.1 cxxflags="/std:c++latest" test-timeout-fail  
  
# this will run the test after b2 compilation with additional options  
../../../bin.v2/libs/test/test/test-timeout-fail.test/XXXX/release/threading-multi/visibility-hidden/test-timeout-fail --log_level=all  
```  
  
Please paste the output of the last command.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-03-03 17:52:56 UTC  
> Url: https://github.com/boostorg/test/issues/208#issuecomment-469046794  

I think the problem come from the definition of the macro `_WIN32_WINNT`.  
  
Would it be possible for you to test the branch `topic/fix-win32-timed-test` and report me any issue?  
  
Thanks!

---

## Comment 4

> Username: spacelg  
> Created at: 2019-03-04 07:03:10 UTC  
> Url: https://github.com/boostorg/test/issues/208#issuecomment-469140996  

Thanks for your reply!   
  
I've changed the content of that file $boost/libs/test/test/writing-test-ts/test-timeout-fail.cpp and increased the sleep time to 5000 and reran it also failed.  
  
For running the single test:  
D:\Boost\out\x64rel\boost\bin.v2\libs\test\test\test-timeout-fail.test\msvc-14.1\release\threading-multi>test-timeout-fail.exe --log_level=all  
Running 1 test case...  
Entering test module "timeout-error"  
libs\test\test\writing-test-ts\test-timeout-fail.cpp(15): Entering test case "test_fail"  
libs/test/test/writing-test-ts/test-timeout-fail.cpp(18): info: check true has passed  
unknown location(0): fatal error: in "test_fail": timeout while executing function  
libs\test\test\writing-test-ts\test-timeout-fail.cpp(15): last checkpoint: "test_fail" fixture dtor  
libs\test\test\writing-test-ts\test-timeout-fail.cpp(15): Leaving test case "test_fail"; testing time: 5014403us  
Leaving test module "timeout-error"; testing time: 5019378us  
  
*** 1 failure is detected in the test module "timeout-error"  
  
This issue was fixed on topic/fix-win32-timed-test branch ?   
Could you prodvid a link of this branch?  
  
Thanks,  
Lin

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2019-03-04 19:56:43 UTC  
> Url: https://github.com/boostorg/test/issues/208#issuecomment-469398599  

@spacelg thanks for the update. My guess is that the `WINNT` is not defined, which prevents the support of the timed tests for win32. I've made a fix in the branch I mentioned.   
  
To get that branch and that test, this should be more or less this:  
```  
cd D:\Boost\out\x64rel\boost\bin.v2\libs\test\  
git fetch --all --prune  
git checkout topic/fix-win32-timed-test branch  
cd test  
..\..\..\b2.exe test-timeout-fail  
```

---

## Comment 6

> Username: spacelg  
> Created at: 2019-03-05 08:05:44 UTC  
> Url: https://github.com/boostorg/test/issues/208#issuecomment-469580186  

Thanks for your information! We reran it under topic/fix-win32-timed-test as below:  
  
D:\Boost\src\libs\test>git.exe checkout topic/fix-win32-timed-test  
Switched to branch 'topic/fix-win32-timed-test'  
Your branch is up-to-date with 'origin/topic/fix-win32-timed-test'.  
  
D:\Boost\src\libs\test>git.exe branch  
  develop  
* topic/fix-win32-timed-test  
  
D:\Boost\src\libs\test>cd test  
  
D:\Boost\src\libs\test\test>..\..\..\b2.exe test-timeout-fail  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : no  (cached)  
    - symlinks supported       : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_thread : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_chrono : yes (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : no  (cached)  
    - Boost.Config Feature Check: cxx11_hdr_thread : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_chrono : yes (cached)  
...patience...  
...found 2050 targets...  
...updating 26 targets...  
compile-c-c++ ..\..\..\bin.v2\libs\test\test\test-timeout-fail.test\msvc-14.1\debug\threading-multi\writing-test-ts\test-timeout-fail.obj  
test-timeout-fail.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\compiler_log_formatter.obj  
compiler_log_formatter.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\debug.obj  
debug.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\decorator.obj  
decorator.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\execution_monitor.obj  
execution_monitor.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\framework.obj  
framework.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\plain_report_formatter.obj  
plain_report_formatter.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\progress_monitor.obj  
progress_monitor.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\results_collector.obj  
results_collector.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\results_reporter.obj  
results_reporter.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\test_framework_init_observer.obj  
test_framework_init_observer.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\test_tools.obj  
test_tools.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\test_tree.obj  
test_tree.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\unit_test_log.obj  
unit_test_log.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\unit_test_main.obj  
unit_test_main.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\unit_test_monitor.obj  
unit_test_monitor.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\unit_test_parameters.obj  
unit_test_parameters.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\junit_log_formatter.obj  
junit_log_formatter.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\xml_log_formatter.obj  
xml_log_formatter.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\xml_report_formatter.obj  
xml_report_formatter.cpp  
msvc.archive ..\..\..\bin.v2\libs\test\build\msvc-14.1\debug\link-static\threading-multi\libboost_unit_test_framework-vc141-mt-gd-x32-1_70.lib  
msvc.link ..\..\..\bin.v2\libs\test\test\test-timeout-fail.test\msvc-14.1\debug\threading-multi\test-timeout-fail.exe  
msvc.manifest ..\..\..\bin.v2\libs\test\test\test-timeout-fail.test\msvc-14.1\debug\threading-multi\test-timeout-fail.exe  
testing.capture-output ..\..\..\bin.v2\libs\test\test\test-timeout-fail.test\msvc-14.1\debug\threading-multi\test-timeout-fail.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
unknown location(0): fatal error: in "test_fail": timeout while executing function  
writing-test-ts\test-timeout-fail.cpp(15): last checkpoint: "test_fail" fixture dtor  
  
*** 1 failure is detected in the test module "timeout-error"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
...removing ..\..\..\bin.v2\libs\test\test\test-timeout-fail.test\msvc-14.1\debug\threading-multi\test-timeout-fail.run  
(failed-as-expected) ..\..\..\bin.v2\libs\test\test\test-timeout-fail.test\msvc-14.1\debug\threading-multi\test-timeout-fail.run  
**passed** ..\..\..\bin.v2\libs\test\test\test-timeout-fail.test\msvc-14.1\debug\threading-multi\test-timeout-fail.test  
...updated 26 targets...  
  
  
It prints this error:  
====== BEGIN OUTPUT ======  
Running 1 test case...  
unknown location(0): fatal error: in "test_fail": timeout while executing function  
writing-test-ts\test-timeout-fail.cpp(15): last checkpoint: "test_fail" fixture dtor  
  
*** 1 failure is detected in the test module "timeout-error"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
  
Is this error expectted to fail ?  
  
Thanks,  
Lin

---

## Comment 7

> Username: raffienficiaud  
> Created at: 2019-03-05 20:50:33 UTC  
> Url: https://github.com/boostorg/test/issues/208#issuecomment-469851922  

Thanks for the quick feedback. Indeed, the test is supposed to fail and we are capturing the fact that boost.test correctly detects this.

---

## Comment 8

> Username: spacelg  
> Created at: 2019-03-07 09:02:51 UTC  
> Url: https://github.com/boostorg/test/issues/208#issuecomment-470442654  

Thanks for your reply!  
  
At present, this issue can now be reproduced on x86.  
  
ErrorMessage:  
testing.capture-output ..\out\x86rel\boost\bin.v2\libs\test\test\test-timeout-fail.test\msvc-14.1\release\threading-multi\test-timeout-fail.run  
        1 file(s) copied.  
...failed testing.capture-output ..\out\x86rel\boost\bin.v2\libs\test\test\test-timeout-fail.test\msvc-14.1\release\threading-multi\test-timeout-fail.run...  
  
[log_x86_test_104.log](https://github.com/boostorg/test/files/2940333/log_x86_test_104.log)  
  
Thanks,  
Lin

---

## Comment 9

> Username: raffienficiaud  
> Created at: 2019-03-07 21:55:25 UTC  
> Url: https://github.com/boostorg/test/issues/208#issuecomment-470710310  

If you are able to run this test locally, I would be thankfull if you would paste the output of the test itself. From the same folder:  
  
```  
..\out\x86rel\boost\bin.v2\libs\test\test\test-timeout-fail.test\msvc-14.1\release\threading-multi\test-timeout-fail --log_level=all  
```  
  
Thanks! (I am struggling reproducing this on my end)

---

## Comment 10

> Username: spacelg  
> Created at: 2019-03-08 07:37:28 UTC  
> Url: https://github.com/boostorg/test/issues/208#issuecomment-470835217  

Thanks for your information!  
I reran it as below:  
  
D:\Boost\out\x86rel\boost\bin.v2\libs\test\test\test-timeout-fail.test\msvc-14.1\release\threading-multi>test-timeout-fail.exe --log_level=all  
Running 1 test case...  
Entering test module "timeout-error"  
libs\test\test\writing-test-ts\test-timeout-fail.cpp(15): Entering test case "test_fail"  
libs/test/test/writing-test-ts/test-timeout-fail.cpp(18): info: check true has passed  
unknown location(0): fatal error: in "test_fail": timeout while executing function  
libs\test\test\writing-test-ts\test-timeout-fail.cpp(15): last checkpoint: "test_fail" fixture dtor  
libs\test\test\writing-test-ts\test-timeout-fail.cpp(15): Leaving test case "test_fail"; testing time: 2030779us  
Leaving test module "timeout-error"; testing time: 2041624us  
  
*** 1 failure is detected in the test module "timeout-error"

---

## Comment 11

> Username: raffienficiaud  
> Created at: 2019-03-08 10:47:43 UTC  
> Url: https://github.com/boostorg/test/issues/208#issuecomment-470886838  

Thank you very much, we are making progress there!  
So the test is properly waiting, but the time-out is not caught by boost.test for some reason. Just to make sure that the macro are properly defined, would it be possible for you to add the following in the .cpp file of the test:  
  
```  
#include <ios>  
BOOST_AUTO_TEST_CASE(test_fail, * utf::timeout(1))  
{  
#if defined(_WIN32_WINNT)  
    std::cout << "_WIN32_WINNT=" << std::hex << _WIN32_WINNT << std::endl;  
#else  
   std::cout << "_WIN32_WINNT not defined" << std::endl;  
#endif  
  
#if defined(BOOST_TEST_WIN32_WAITABLE_TIMERS)  
    std::cout << "BOOST_TEST_WIN32_WAITABLE_TIMERS defined" << std::endl;  
#else  
   std::cout << "BOOST_TEST_WIN32_WAITABLE_TIMERS not defined" << std::endl;  
#endif  
  
    std::this_thread::sleep_for(std::chrono::milliseconds(2000));  
    BOOST_TEST(true);  
}  
```  
  
and rerun the test as before, the first time from `b2` to compile and run it, the second time with the direct command line call with `--log_level=all` (previous comment).  
  
Thanks!

---

## Comment 12

> Username: spacelg  
> Created at: 2019-03-12 08:36:40 UTC  
> Url: https://github.com/boostorg/test/issues/208#issuecomment-471905469  

Thanks for your reply!  
  
If the fixed branch is merged into https://github.com/boostorg/boost/commits/master branch, please let us know.  
  
Thanks,  
Lin

---

## Comment 13

> Username: raffienficiaud  
> Created at: 2019-03-12 09:07:21 UTC  
> Url: https://github.com/boostorg/test/issues/208#issuecomment-471915085  

Master contains the fix about the `_WINNT` definition. However I understood that you are still reproducing the issue after that fix. Please give a try to master. If it does not work, it would be good that you apply the changes I mentioned in my last comment to dig more.  
  
Many thanks!

---

## Comment 14

> Username: raffienficiaud  
> Created at: 2019-03-24 18:57:14 UTC  
> Url: https://github.com/boostorg/test/issues/208#issuecomment-475988671  

Any update? thanks

---

## Comment 15

> Username: raffienficiaud  
> Created at: 2019-10-05 10:30:19 UTC  
> Url: https://github.com/boostorg/test/issues/208#issuecomment-538637920  

Closing the issue as I believe this has been fixed. Please reopen with explanations if this is not the case. Thanks!
