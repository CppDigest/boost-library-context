# #20 [CMake] Generate cmake target that other libraries can use [Merged]

> Username: Mike-Devel  
> Created at: 2018-09-17 09:32:14 UTC  
> Updated at: 2018-10-23 17:23:47 UTC  
> Merged at: 2018-10-23 13:49:21 UTC  
> Closed at: 2018-10-23 13:49:21 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/20  

... to express their dependency on this library and retrieve any  
configuration information such as the include directory, binary  
to link to (if any), necessary compiler options or the required  
c++ standards level.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-09-27 15:02:01 UTC  
> Updated_at: 2018-09-27 15:02:30 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/20#issuecomment-425126694  

I need a test, which can be invoked using the b2 framework which Boost currently uses  to run tests for each library, which shows this PR actually working in a CMake project. I would heavily prefer the test to be one of the tests in the preprocessor test jamfile and not in some overall Boost jamfile because it is important to run such a test as part of preprocessor testing to verify that this PR actually works.

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2018-10-16 07:55:28 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/20#issuecomment-430138385  

@eldiener: Before I finally try to tackle this: Is a test as part of travis and appveyor enough or does it actually have to be invoked from the b2 framework?

---

## Comment 3

> Username: eldiener  
> Created_at: 2018-10-16 13:45:44 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/20#issuecomment-430243304  

It needs to be invokable locally in some way by a maintainer of the library so he can see that the results pass the test. If you can not get this to work as part of the b2 tests it must still work as part of some sort of test in which the maintainer can locally see the result as successful or not. The two main reasons I have suggested that this be tested as part of b2 is:  
  
1) If the maintainer locally tests his library when some change has been made, as I do with a number of compilers, he can see the results as part of invoking b2 against his jamfile in the test directory.  
2) The test is incorporated in the current Boost regression testing framework.  
  
However if it is impossible, or too much work, to incorporate the test as a b2 test, some other way of testing the viability of the PR locally must exist, even if it just uses CMake itself outside of b2.

---

## Comment 4

> Username: Mike-Devel  
> Created_at: 2018-10-16 18:08:10 UTC  
> Updated_at: 2018-10-17 10:43:51 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/20#issuecomment-430339686  

Thanks for the clear guidance.   
  
I haven't yet figured out a clean and simple way to integrate this into the b2 test (after all, the test should not be much more complicated than what it is testing), but I think that speaks more to my unfamiliarity with b2 than any fundamental problems with this task, There were some good suggestions on the mailing list that I have to explore.  
  
For now, I just added a job to travis to show the general structure of the test. If you want to run the test locally, you can just run the following ~~three~~ four commands:  
  
        - mkdir __build__   
        - cd __build__  
        - cmake <libroot>/test  
        - cmake --build .  
  
I hope I can provide a solution that runs as part of b2 test soon (unfortunately, I don't have a lot of time for this at the moment).

---

## Comment 5

> Username: eldiener  
> Created_at: 2018-10-16 18:54:43 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/20#issuecomment-430356144  

From Appveyor:  
  
mkdir libs/preprocessor/test/__build__cmake__  
The syntax of the command is incorrect.  
Command exited with code 1

---

## Comment 6

> Username: Mike-Devel  
> Created_at: 2018-10-16 19:18:34 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/20#issuecomment-430364710  

@eldiener: Sorry, please ignore that commit. It was just a quick try and should have been on a private branch and not spam your inbox. For now there is only a travis check (ee5ed25 is the relavant commit). I'll mirror this for appveyor but that'll have to wait for tomorrow.

---

## Comment 7

> Username: Mike-Devel  
> Created_at: 2018-10-17 14:02:51 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/20#issuecomment-430641042  

Ok, so I added tests for the cmake file to both appveyor and travis (the failure in travis seems to be due to an error in develop itself). I wasn't able to come up with a proper solution for b2 build yet but maybe you can already comment on the existing tests when you have the time.

---

## Comment 8

> Username: Mike-Devel  
> Created_at: 2018-10-19 10:18:18 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/20#issuecomment-431316553  

@eldiener: OK, so in my opinion it would be best to merge this PR as is, which means:  
- The cmake file is tested as part of travis and appveyor  
- It can be tested locally via the command sequence:   
  
      - mkdir __build__   
      - cd __build__  
      - cmake <libroot>/test  
      - cmake --build .  
  
- It is NOT tested as part of `b2 test`  
  
I have a commit that also runs this as part of b2 (https://github.com/Mike-Devel/preprocessor/commit/0516fe051cdc4dff3949cae190c40c5c5c9d15b1) but  
  
- It always runs this test an generates quite some noise on the console, even if nothing has changed since the last run.  
- Part of the functionality of this CMake file is that it doesn't require b2 to run first (and e.g. copy headers around) which is difficult (maybe impossible) to test as part of `b2 test` anyway.  
  
Maybe this can easily be solved by someone more familiar with b2 than me, but I have the feeling that the complexity of the test starts to dramatically outgrow the complexity of what is being tested. (I feel like writing a 100 line unit-test for a `int add(int x, int y)` function). I don't think this will serve maintainability.  
  
I'd however be happy to adapt the travis and/or appveyor changes if you don't like them (btw. the fail on travis seems to be unrelated to my changes considering that `develop` is failing with the same error and the cmake self test job is actually the only one that passes) .

---

## Comment 9

> Username: eldiener  
> Created_at: 2018-10-19 21:14:51 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/20#issuecomment-431501090  

The fail on Travis is my fault and I have fixed it. Let me take a look at this and I will comment once I understand it.

---

## Comment 10

> Username: eldiener  
> Created_at: 2018-10-19 21:37:22 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/20#issuecomment-431506277  

A few questions about CMake since I am just a beginner:  
  
1) Does CMake run anything if nothing has changed.  
2) What does it mean when CMake fails ? Is there an error indication when either the CMake scripts are erroneous to run a test or when a test run by CMake itself fails ?  
  
Relating this to Boost Build, b2 will put out errors if either the jamfile is incorrect or when a test fails. A test failing for a compile test means that there is a compilation error and a test failing in a run test means either that an exception was thrown or that the main routine returned something other than 0.  
  
If we can incorporate the CMake test for this PR in the test jamfile it would be nice if the result of the test showed an error if one actually occurs. As to the fact that it would run all the time, rather than when anything had changed to any of the files in preprocessor I can live with that as this is only a test of your PR and not a test of any particular preprocessor header file; but it would be ideal if a test of your PR in the jamfile would only be run if either CMakeLists.txt in your PR actually changed.

---

## Comment 11

> Username: Mike-Devel  
> Created_at: 2018-10-20 09:53:23 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/20#issuecomment-431566406  

> A few questions about CMake since I am just a beginner:  
  
No problem. Just so I don't bore you with too much things you already know: Have you ever used cmake to compile more than a "Hello World"-Project?  
  
> Does CMake run anything if nothing has changed.  
  
No, but it will print a few lines telling you that it didn't to anything.  
  
> What does it mean when CMake fails ? Is there an error indication when either the CMake scripts are erroneous to run a test or when a test run by CMake itself fails ?  
  
Currently, this cmake file does NOT run any tests.   
Let's go at this step by step (sorry If I repeat a lot fo things you already know).  
  
- The purpose of the cmake file in the preprocessor root directory is to create a cmake target called `Boost::preprocessor` that other cmake projects can "link" to (in case of a header-only library that just means that the include directory of Boost.Preprocessor is added to the compiler flags of the project using Boost::preprocessor).  
- Essentially there could be two things wrong with this file  
  1. There might be a syntactic error (i.e. it is an invalid cmake file)  
  2. It could attach the wrong include directory to the target here::   
     https://github.com/boostorg/preprocessor/blob/344ce6abf9584a5e082e3851bf073b911ac08acf/CMakeLists.txt#L11  
  
- The first kind of error can be detected by just running `cmake <dir-of-cmake-file>` (just as running a compiler over an invalid cpp file). There is probably a way to detect the second kind of mistake, but the more robust and generic solution is (as suggested by you) to simply to check if the cmake file serves its purpose by using it in a dummy cmake project. That is what the CMakeLists.txt file in `preprocessor/test` is for:  
  - It first includes the CMakeLists.txt file in the `preprocessor` root directory in order to get access to the `Boost::preprocessor` target:  
https://github.com/boostorg/preprocessor/blob/344ce6abf9584a5e082e3851bf073b911ac08acf/test/CMakeLists.txt#L12   
  - Then it creates an exectuable `boost_preprocessor_cmake_self_test(.exe)`  with `config_info.cpp` as a source file and Boost::preprocessor as a dependency:  
https://github.com/boostorg/preprocessor/blob/344ce6abf9584a5e082e3851bf073b911ac08acf/test/CMakeLists.txt#L14-L15  
  
- Actually compiling the dummy-app (and thus testing the cmake files) involves running two commands, which can fail for different reasons:  
  
  - `cmake <preprocessor/root>` will process the cmake files and generate the appropriate build files (by default make files on linux and vs project files on windows).   
If that step fails it is most likely due to a syntactic mistake in one of the cmake files or because the dummy app tries to link to a non-existing target (e.g. due to a typo)  
  - `cmake --build .` will run call the actual build system to compile the executable.   
If this step fails it is either because there is an error in  `config_info.cpp` or because the compiler can't resolve some of the includes (e.g. because we attached the wrong include directory to `Boost::preprocessor`).  
  
> If we can incorporate the CMake test for this PR in the test jamfile it would be nice if the result of the test showed an error if one actually occurs.   
  
From the perspective of b2 we would be running a couple of shell commands that succeed or fail. If one of them fails we should see two errors: One from CMake itself and one from b2 saying that the command failed. I think that is exactly what you want. But there might be a more elegant way to do this than what I did in the linked commit.  
  
> As to the fact that it would run all the time, rather than when anything had changed to any of the files in preprocessor I can live with that as this is only a test of your PR and not a test of any particular preprocessor header file; but it would be ideal if a test of your PR in the jamfile would only be run if either CMakeLists.txt in your PR actually changed.  
  
In order to do that we would have to tell b2 to only run the shell commands if one of the CMake files change, but I don't really know how to do that (I'm sure it is simple, but I'd prefer if I didn't have to learn the ins-and outs of b2).

---

## Comment 12

> Username: Mike-Devel  
> Created_at: 2018-10-23 09:17:13 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/20#issuecomment-432166553  

I know you currently have bigger fish to fry, such as the new msvc preprocessor mode, but it would be great if this could get merged before the boost feature freeze.

---

## Comment 13

> Username: Mike-Devel  
> Created_at: 2018-10-23 17:23:46 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/20#issuecomment-432340874  

Thank you very much!

---
