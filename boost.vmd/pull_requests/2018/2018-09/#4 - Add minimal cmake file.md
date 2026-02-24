# #4 [CMake] Add minimal cmake file [Merged]

> Username: Mike-Devel  
> Created at: 2018-09-25 17:07:53 UTC  
> Updated at: 2018-11-05 15:08:14 UTC  
> Merged at: 2018-10-29 00:42:09 UTC  
> Closed at: 2018-10-29 00:42:10 UTC  
> Url: https://github.com/boostorg/vmd/pull/4  

Generate cmake target that can  
be used by other libraries to express their dependency on  
this library and retrieve any configuration information  
such as the include directory, transitive dependencies,  
necessary compiler options or the required c++ standards level.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-09-27 15:03:30 UTC  
> Url: https://github.com/boostorg/vmd/pull/4#issuecomment-425127253  

I need a test, which can be invoked using the b2 framework which Boost currently uses to run tests for each library, which shows this PR actually working in a CMake project. I would heavily prefer the test to be one of the tests in the vmd test jamfile and not in some overall Boost jamfile because it is important to run such a test as part of vmd testing to verify that this PR actually works.

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2018-09-27 15:14:48 UTC  
> Url: https://github.com/boostorg/vmd/pull/4#issuecomment-425131588  

Fair enough. Is there a good tutorial on how to execute a bunch of shell commands from a b2 test?

---

## Comment 3

> Username: eldiener  
> Created_at: 2018-09-27 15:17:47 UTC  
> Url: https://github.com/boostorg/vmd/pull/4#issuecomment-425132749  

I do not think there is a tutorial but I believe there is a way to call the command processor to execute a batch file on Windows or a shell program on Linux. I am not sure what the Mac has as far as batch processing.

---

## Comment 4

> Username: Mike-Devel  
> Created_at: 2018-09-27 15:20:08 UTC  
> Url: https://github.com/boostorg/vmd/pull/4#issuecomment-425133563  

However, as this library has a dependency on boost::preprocessor you can't test it without that library being present and having a cmake file too (at least in order to be realistic).

---

## Comment 5

> Username: eldiener  
> Created_at: 2018-09-27 20:46:52 UTC  
> Url: https://github.com/boostorg/vmd/pull/4#issuecomment-425236927  

Regarding the boost::preprocessor library being present isn't CMake responsible for finding that library when the proper CMakeLists.txt exists for boost::preprocessor ? Regarding the cmake file for boost::preprocessor I have already answered in the PR you created there that I need a test in preprocessor which verifies the correctness of the PR in order to accept the PR there.

---

## Comment 6

> Username: Mike-Devel  
> Created_at: 2018-09-27 21:50:59 UTC  
> Updated_at: 2018-10-01 09:30:50 UTC  
> Url: https://github.com/boostorg/vmd/pull/4#issuecomment-425254608  

> Regarding the boost::preprocessor library being present isn't CMake responsible for finding that library when the proper CMakeLists.txt exists for boost::preprocessor ?   
  
Isn't that what I said? I just wanted to point ot that there is a dependency in case this wasn't clear.   
  
> Regarding the cmake file for boost::preprocessor I have already answered in the PR you created there that I need a test in preprocessor which verifies the correctness of the PR in order to accept the PR there.  
  
I know.  I just didn't answer there, as it would have been pretty much the same as here.

---

## Comment 7

> Username: eldiener  
> Created_at: 2018-10-26 22:53:20 UTC  
> Url: https://github.com/boostorg/vmd/pull/4#issuecomment-433563906  

I have merged your PR to add your CMake files for preprocessor so if you can likewise provide a test for VMD I would merge your PR for VMD.

---

## Comment 8

> Username: eldiener  
> Created_at: 2018-10-28 06:20:25 UTC  
> Url: https://github.com/boostorg/vmd/pull/4#issuecomment-433679865  

The Travis CI build has:  
  
cmake ../vmd/test/test_cmake  
CMake Error: The source directory "/home/travis/build/boostorg/vmd/vmd/test/test_cmake" does not exist.

---

## Comment 9

> Username: Mike-Devel  
> Created_at: 2018-10-28 09:30:20 UTC  
> Url: https://github.com/boostorg/vmd/pull/4#issuecomment-433689508  

Yes, Sorry I should have mentioned I'm not done yet. I'm currently testing the latest changes on a separate branch and will notify them when I incorporated them here.

---

## Comment 10

> Username: Mike-Devel  
> Created_at: 2018-10-28 09:43:06 UTC  
> Url: https://github.com/boostorg/vmd/pull/4#issuecomment-433690258  

Btw.: Did you have time to give the changes a look? I made some structureal changes compared to the test in preprocessor test.   
E.g. the CMakeLists.txt file that defines the dummy project using Boost::vmd resides in a separate subdirectory in test. This is to avoid people expecting the file to run the boost vmd unit tests (conventionally that is done from a CMakeLists.txt file that gets called from the vmd root file and located in vmd/test.  
  
Also, just as in boost preprocessor, the changes I made to travis and particular appveyor.yml are somewhat invasive. I'm not an expert in those, so there might be a more elegant way to achieve the same result.

---

## Comment 11

> Username: Mike-Devel  
> Created_at: 2018-10-28 12:40:01 UTC  
> Url: https://github.com/boostorg/vmd/pull/4#issuecomment-433701715  

@eldiener: OK, Everything should be fine now (travis had some issues today, which is why it took me much longer than expected). If you are ok with the changes, you can merge the PR once travis is green.

---
