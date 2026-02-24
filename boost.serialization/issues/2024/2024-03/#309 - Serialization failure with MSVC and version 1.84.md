# #309 - Serialization failure with MSVC and version 1.84 [Closed]

> Username: johnwason  
> Created at: 2024-03-14 16:15:58 UTC  
> Updated at: 2024-07-27 03:42:13 UTC  
> Closed at: 2024-07-27 03:42:13 UTC  
> Url: https://github.com/boostorg/serialization/issues/309  

We are seeing a new issue with serialization using MSVC and version 1.84. Unit tests are failing, and it looks like the serialization is being corrupted with binary archives, and some tests are failing with XML archives. The serialization has worked with previous versions of serialization. I did a matrix test using version 1.82, 1.83, and 1.84 to confirm. 1.82 and 1.83 passed, while 1.84 failed. Here is the workflow run:  
  
https://github.com/johnwason/tesseract/actions/runs/8242279639  
  
The upstream source repository is here: https://github.com/tesseract-robotics/tesseract  
  
Both tesseract and boost-serialization are large projects so I am not sure where the problem is. Were there any major changes in boost-serialization that would explain the problem?  
  
@Levi-Armstrong

---

## Comment 1

> Username: robertramey  
> Created at: 2024-03-14 18:26:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-1998073202  

I looked at the links cited.  I'm not sure how to understand the build script and how/if it points to the boost serialization library.

---

## Comment 2

> Username: johnwason  
> Created at: 2024-03-14 19:25:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-1998259994  

This is the test that is failing: https://github.com/tesseract-robotics/tesseract/blob/master/tesseract_environment/test/tesseract_environment_serialization_unit.cpp  
  
This contains the test utility functions that implement serialization: https://github.com/tesseract-robotics/tesseract/blob/master/tesseract_common/include/tesseract_common/unit_test_utils.h

---

## Comment 3

> Username: LowLevelMahn  
> Created at: 2024-03-25 06:50:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2017336384  

im currently upgrading to 1.84 - so just commenting here to be informed by github if there is a real issue

---

## Comment 4

> Username: johnwason  
> Created at: 2024-04-11 00:48:13 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2048672251  

I have narrowed down the problem to the "command" class tests, and tests that use commands.  
  
An example of the test failure is `EnvironmentCommandsSerializeUnit.AddLinkCommand` in the test function `testSerializationDerivedClass()` located here: https://github.com/tesseract-robotics/tesseract/blob/ea3d5c37d466d55068a41e75de24ce2655f5c648/tesseract_environment/test/tesseract_environment_serialization_unit.cpp#L145  
  
Within `testSerializationDerivedClass()` the failure is here: https://github.com/tesseract-robotics/tesseract/blob/ea3d5c37d466d55068a41e75de24ce2655f5c648/tesseract_common/include/tesseract_common/unit_test_utils.h#L145  
  
As far as I can tell this looks like a failure related to polymorphic types. This error was not occurring in previous boost serialization versions.  
  
The base class `Command` is defined here: https://github.com/tesseract-robotics/tesseract/blob/ea3d5c37d466d55068a41e75de24ce2655f5c648/tesseract_environment/include/tesseract_environment/command.h#L81  
  
And the example subclass `AddLinkCommand` is located here: https://github.com/tesseract-robotics/tesseract/blob/ea3d5c37d466d55068a41e75de24ce2655f5c648/tesseract_environment/include/tesseract_environment/commands/add_link_command.h#L45  
  
The implementation for the commands is located here: https://github.com/tesseract-robotics/tesseract/blob/ea3d5c37d466d55068a41e75de24ce2655f5c648/tesseract_environment/src/command.cpp#L35   
  
https://github.com/tesseract-robotics/tesseract/blob/ea3d5c37d466d55068a41e75de24ce2655f5c648/tesseract_environment/src/commands/add_link_command.cpp#L41  
  
There are many other tests failing that all use the `Command` base class.  
  
@Levi-Armstrong

---

## Comment 5

> Username: johnwason  
> Created at: 2024-04-11 01:15:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2048737498  

Note that the serialization to string and XML works correctly so I don't think the issue is in the tesseract library.

---

## Comment 6

> Username: johnwason  
> Created at: 2024-04-12 00:28:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2050761195  

I did more tinkering and was able to find this difference between the failing unit test and an isolated example that seems to work correctly. `ChangeJointPositionLimitsCommand2.binary` is from the failing test, and `ChangeJointPositionLimitsCommand3.binary` is from the working test. The only difference is in like 60:  
  
```  
$ xxd ChangeJointPositionLimitsCommand2.binary  
00000000: 1600 0000 0000 0000 7365 7269 616c 697a  ........serializ  
00000010: 6174 696f 6e3a 3a61 7263 6869 7665 1400  ation::archive..  
00000020: 0404 0408 0100 0000 0001 0000 0002 0020  ...............   
00000030: 0000 0000 0000 0043 6861 6e67 654a 6f69  .......ChangeJoi  
00000040: 6e74 506f 7369 7469 6f6e 4c69 6d69 7473  ntPositionLimits  
00000050: 436f 6d6d 616e 6401 0000 0000 0000 0000  Command.........  
00000060: 0000 0000 0001 0000 000c 0000 0000 0000  ................  
00000070: 0000 0100 0000 0000 0000 0800 0000 0000  ................  
00000080: 0000 0000 0000 0000 0000 0006 0000 0000  ................  
00000090: 0000 006a 6f69 6e74 3600 0000 0000 182d  ...joint6......-  
000000a0: 4454 fb21 09c0 182d 4454 fb21 0940       DT.!...-DT.!.@  
  
$ xxd ChangeJointPositionLimitsCommand3.binary  
00000000: 1600 0000 0000 0000 7365 7269 616c 697a  ........serializ  
00000010: 6174 696f 6e3a 3a61 7263 6869 7665 1400  ation::archive..  
00000020: 0404 0408 0100 0000 0001 0000 0002 0020  ...............  
00000030: 0000 0000 0000 0043 6861 6e67 654a 6f69  .......ChangeJoi  
00000040: 6e74 506f 7369 7469 6f6e 4c69 6d69 7473  ntPositionLimits  
00000050: 436f 6d6d 616e 6401 0000 0000 0000 0000  Command.........  
00000060: 0100 0000 0001 0000 000c 0000 0000 0000  ................  
00000070: 0000 0100 0000 0000 0000 0800 0000 0000  ................  
00000080: 0000 0000 0000 0000 0000 0006 0000 0000  ................  
00000090: 0000 006a 6f69 6e74 3600 0000 0000 182d  ...joint6......-  
000000a0: 4454 fb21 09c0 182d 4454 fb21 0940       DT.!...-DT.!.@  
```  
  
The serialization step is here: https://github.com/johnwason/tesseract/blob/51a62b5bf3d844f8605b76879ac0f4c38b7d059b/tesseract_environment/src/commands/change_joint_position_limits_command.cpp#L81 and the class is defined here: https://github.com/johnwason/tesseract/blob/51a62b5bf3d844f8605b76879ac0f4c38b7d059b/tesseract_environment/include/tesseract_environment/commands/change_joint_position_limits_command.h#L45  
  
Maybe someone with more knowledge of the serialization format can explain what could cause this difference.

---

## Comment 7

> Username: johnwason  
> Created at: 2024-04-18 22:22:42 UTC  
> Updated at: 2024-04-18 22:24:03 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2065424496  

I have been able to isolate and reproduce the problem.  
  
Here is the repo with the test code: https://github.com/johnwason/boost_serialization_1_84_bug  
  
And here is the action run: https://github.com/johnwason/boost_serialization_1_84_bug/actions/runs/8745401644/job/24000327991  
  
What I have discovered is that if the classes being serialized is in a shared library, then the serialization will fail. The failure only occurs for binary archives. The action run tests version 1.83 and version 1.82 of boost serialization, and it appears to work correctly.  
  
Here is the cmake file. The tests for test_no_lib_prog works, while test_lib_prog fails.  
  
```cmake  
cmake_minimum_required(VERSION 3.15.0)  
project(boost_serialization_1_84_bug LANGUAGES CXX)  
  
set(CMAKE_CXX_STANDARD 17)  
  
find_package(Boost REQUIRED COMPONENTS system filesystem serialization)  
find_package(GTest REQUIRED)  
  
add_library(test_lib SHARED cmd.cpp cmd2.cpp)  
target_link_libraries(test_lib Boost::boost  
Boost::system  
Boost::filesystem  
Boost::serialization  
GTest::GTest GTest::Main)  
set_target_properties(test_lib PROPERTIES WINDOWS_EXPORT_ALL_SYMBOLS TRUE)  
  
add_executable(test_lib_prog test.cpp)  
target_link_libraries(test_lib_prog test_lib Boost::boost  
Boost::system  
Boost::filesystem  
Boost::serialization  
GTest::GTest GTest::Main)  
  
add_executable(test_no_lib_prog test.cpp cmd.cpp cmd2.cpp)  
target_link_libraries(test_no_lib_prog Boost::boost  
Boost::system  
Boost::filesystem  
Boost::serialization  
GTest::GTest GTest::Main)  
  
enable_testing()  
  
add_test(NAME test_no_lib COMMAND test_no_lib_prog)  
add_test(NAME test_lib COMMAND test_lib_prog)  
```  
  
Here is the ctest log:  
  
```  
test 1  
    Start 1: test_no_lib  
  
1: Test command: D:\a\boost_serialization_1_84_bug\boost_serialization_1_84_bug\build\Release\test_no_lib_prog.exe  
1: Working Directory: D:/a/boost_serialization_1_84_bug/boost_serialization_1_84_bug/build  
1: Test timeout computed to be: 10000000  
1: [==========] Running 6 tests from 2 test suites.  
1: [----------] Global test environment set-up.  
1: [----------] 3 tests from serialization_direct_test  
1: [ RUN      ] serialization_direct_test.xml  
1: [       OK ] serialization_direct_test.xml ([20](https://github.com/johnwason/boost_serialization_1_84_bug/actions/runs/8745401644/job/24000327991#step:6:21) ms)  
1: [ RUN      ] serialization_direct_test.binary  
1: [       OK ] serialization_direct_test.binary (0 ms)  
1: [ RUN      ] serialization_direct_test.xml_string  
1: [       OK ] serialization_direct_test.xml_string (0 ms)  
1: [----------] 3 tests from serialization_direct_test ([21](https://github.com/johnwason/boost_serialization_1_84_bug/actions/runs/8745401644/job/24000327991#step:6:22) ms total)  
1:   
1: [----------] 3 tests from serialization_derived_test  
1: [ RUN      ] serialization_derived_test.xml  
1: [       OK ] serialization_derived_test.xml (1 ms)  
1: [ RUN      ] serialization_derived_test.binary  
1: [       OK ] serialization_derived_test.binary (0 ms)  
1: [ RUN      ] serialization_derived_test.xml_string  
1: [       OK ] serialization_derived_test.xml_string (0 ms)  
1: [----------] 3 tests from serialization_derived_test (2 ms total)  
1:   
1: [----------] Global test environment tear-down  
1: [==========] 6 tests from 2 test suites ran. ([24](https://github.com/johnwason/boost_serialization_1_84_bug/actions/runs/8745401644/job/24000327991#step:6:25) ms total)  
1: [  PASSED  ] 6 tests.  
1/2 Test #1: test_no_lib ......................   Passed    0.08 sec  
test 2  
    Start 2: test_lib  
  
2: Test command: D:\a\boost_serialization_1_84_bug\boost_serialization_1_84_bug\build\Release\test_lib_prog.exe  
2: Working Directory: D:/a/boost_serialization_1_84_bug/boost_serialization_1_84_bug/build  
2: Test timeout computed to be: 10000000  
2: [==========] Running 6 tests from 2 test suites.  
2: [----------] Global test environment set-up.  
2: [----------] 3 tests from serialization_direct_test  
2: [ RUN      ] serialization_direct_test.xml  
2: [       OK ] serialization_direct_test.xml (1 ms)  
2: [ RUN      ] serialization_direct_test.binary  
2: D:\a\boost_serialization_1_84_bug\boost_serialization_1_84_bug\unit_test_utils.h(73): error: Value of: *object_derived != *nobject_derived  
2:   Actual: true  
2: Expected: false  
2:   
2: [  FAILED  ] serialization_direct_test.binary (0 ms)  
2: [ RUN      ] serialization_direct_test.xml_string  
2: [       OK ] serialization_direct_test.xml_string (0 ms)  
2: [----------] 3 tests from serialization_direct_test (2 ms total)  
2:   
2: [----------] 3 tests from serialization_derived_test  
2: [ RUN      ] serialization_derived_test.xml  
2: [       OK ] serialization_derived_test.xml (1 ms)  
2: [ RUN      ] serialization_derived_test.binary  
2: D:\a\boost_serialization_1_84_bug\boost_serialization_1_84_bug\unit_test_utils.h(73): error: Value of: *object_derived != *nobject_derived  
2:   Actual: true  
2: Expected: false  
2:   
2: [  FAILED  ] serialization_derived_test.binary (1 ms)  
2: [ RUN      ] serialization_derived_test.xml_string  
2: [       OK ] serialization_derived_test.xml_string (0 ms)  
2: [----------] 3 tests from serialization_derived_test (3 ms total)  
2:   
2: [----------] Global test environment tear-down  
2: [==========] 6 tests from 2 test suites ran. (6 ms total)  
2: [  PASSED  ] 4 tests.  
2: [  FAILED  ] 2 tests, listed below:  
2: [  FAILED  ] serialization_direct_test.binary  
2: [  FAILED  ] serialization_derived_test.binary  
2:   
2:  2 FAILED TESTS  
2/2 Test #2: test_lib .........................***Failed    0.02 sec  
  
[50](https://github.com/johnwason/boost_serialization_1_84_bug/actions/runs/8745401644/job/24000327991#step:6:51)% tests passed, 1 tests failed out of 2  
  
Total Test time (real) =   0.10 sec  
  
The following tests FAILED:  
	  2 - test_lib (Failed)  
Errors while running CTest  
```

---

## Comment 8

> Username: johnwason  
> Created at: 2024-05-21 21:52:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2123500726  

I am still seeing this failure in boost 1.85.

---

## Comment 9

> Username: johnwason  
> Created at: 2024-06-07 00:26:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2153632318  

Are there any suggestions where I can begin troubleshooting?

---

## Comment 10

> Username: correaa  
> Created at: 2024-06-07 00:44:11 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2153648635  

I can’t help you, I don’t have experience with Windows. but I am curious what you do mean by “classes being serialized is in a shared library”? do you mean that especial members are linked in a dynamic library? dll?

---

## Comment 11

> Username: robertramey  
> Created at: 2024-06-07 01:49:57 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2153709293  

Sorry I haven't been more responsive.  As far as Boost is concerned, I've been bogged down in build/test issues which have nothing to do with the serialization library itself.

---

## Comment 12

> Username: robertramey  
> Created at: 2024-06-07 01:53:25 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2153713567  

You've done some real work here.  I would agree that your experiments with different archives suggest that it's an issue with binary archive suggest that the problem lies there.  That narrows things down a lot.  
  
 Could you send the source code to your tests - both the failing and passing one?

---

## Comment 13

> Username: johnwason  
> Created at: 2024-06-07 01:58:12 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2153717783  

The source code for the isolated test is here: https://github.com/johnwason/boost_serialization_1_84_bug . It includes an actions workflow that does a parametric test of different boost versions.  
  
The original problem was discovered in the Tesseract robot motion planner: https://github.com/tesseract-robotics/tesseract . The isolated test was extracted from this source.

---

## Comment 14

> Username: correaa  
> Created at: 2024-06-07 15:53:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2155110624  

> The source code for the isolated test is here: https://github.com/johnwason/boost_serialization_1_84_bug . It includes an actions workflow that does a parametric test of different boost versions.  
>   
  
Excuse my ignorance, but what is the meaning of "&=" for an Archive in cmd.cpp (line 15 for example) ?

---

## Comment 15

> Username: robertramey  
> Created at: 2024-06-07 16:11:59 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2155139940  

LOL - good question!  I'd be curious to hear the answer as well.

---

## Comment 16

> Username: Levi-Armstrong  
> Created at: 2024-06-08 12:06:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2156009676  

It looks like it is a typo. There are 656 lines of serialization code within Tesseract and only two lines of code has the syntax `ar&=` which is clearly not correct.

---

## Comment 17

> Username: Levi-Armstrong  
> Created at: 2024-06-08 12:15:20 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2156013733  

@robertramey and @correaa Any thoughts why this syntax works at all?

---

## Comment 18

> Username: robertramey  
> Created at: 2024-06-08 16:18:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2156092035  

I'm mystified by this.  I did look into it but didn't see wow it would not produce a syntax error.  I'm going to run your test case with the debugger.  I expect this will address the error you found and also shed light on this.

---

## Comment 19

> Username: johnwason  
> Created at: 2024-06-10 19:52:28 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2159170961  

I changed `&=` to `&` and it appears to have worked?? https://github.com/johnwason/boost_serialization_1_84_bug/actions/runs/9454574752/job/26042427949  
  
I am getting the warning "warning C4552: '&': result of expression not used" now.  Can this be ignored, or is there something else we are missing?  
  
I am going to test the main tesseract library to see if the test pass.

---

## Comment 20

> Username: correaa  
> Created at: 2024-06-10 20:05:01 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2159189849  

I guess that &= was converting either side of the expression to `int`. I am unaware that such conversions are possible, but it is worth checking. Also compiling with all conversion warnings on.

---

## Comment 21

> Username: johnwason  
> Created at: 2024-06-10 20:30:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2159228728  

My isolated test case is fixed, but I am still getting crashes and failures in the main library. I am only seeing this issue for the binary archive so I don't think it is something in the client code.

---

## Comment 22

> Username: correaa  
> Created at: 2024-06-10 20:35:40 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2159237865  

Isolate the error again?

---

## Comment 23

> Username: robertramey  
> Created at: 2024-06-10 22:53:00 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2159441832  

- Thanks to all of for your hard work!  Here's what I do with these tough cases:  
  
1. create test case.  You've done that.  I'm not familiar with GTEST so I might change the test case again.    
2. verify that the test case builds and still fails on my development system.  
3. use the debugger in my IDE to step through the test case.  Since you've got a passing AND a failing instance, it should be fairly easily to compare the two and isolate the error.  It is tedious though.  But it does work.  
4. I would do the same with the &= issue.  This should permit one to trace into any conversions which occur.  when you find something, looking at the stack in the debugger is very useful.  
  
As I've said, I don't see the operator &=defined for an archive type - so I would expect  a syntax error.

---

## Comment 24

> Username: johnwason  
> Created at: 2024-06-11 02:30:25 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2159655078  

It looks like I nerfed the test rather than fixing the error. Here is the corrected run showing that it is still failing:  
  
https://github.com/johnwason/boost_serialization_1_84_bug/actions/runs/9458596594

---

## Comment 25

> Username: johnwason  
> Created at: 2024-06-11 03:14:27 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2159701039  

One possibility I noticed is that there is a singleton template that is being initialized with separate static values in the different libraries. This would mean that the base class is seeing a different singleton than the derived class when trying to serialize. Was something involving singletons changed in the more recent versions?

---

## Comment 26

> Username: Levi-Armstrong  
> Created at: 2024-07-26 16:02:20 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2253059133  

@robertramey and @correaa This is still failing after the fix.

---

## Comment 27

> Username: Levi-Armstrong  
> Created at: 2024-07-26 21:29:20 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2253532616  

I found the other issue. The cpp was missing `BOOST_CLASS_EXPORT_IMPLEMENT(tesseract_environment1::Command)`.

---

## Comment 28

> Username: johnwason  
> Created at: 2024-07-27 03:18:24 UTC  
> Updated at: 2024-07-27 03:18:40 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2253727668  

The fix by @Levi-Armstrong fixed the test case. I am working on the full library test now.  
  
... Why did this work before for other boost versions and different OS??

---

## Comment 29

> Username: johnwason  
> Created at: 2024-07-27 03:42:13 UTC  
> Url: https://github.com/boostorg/serialization/issues/309#issuecomment-2253734564  

This appears to be fixed with https://github.com/tesseract-robotics/tesseract/pull/1035  
  
Closing the issue.
