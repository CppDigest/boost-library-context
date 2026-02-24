# #201 - Document required linker flags [Closed]

> Username: NAThompson  
> Created at: 2019-02-01 19:29:36 UTC  
> Updated at: 2019-02-02 19:08:58 UTC  
> Closed at: 2019-02-02 19:03:36 UTC  
> Url: https://github.com/boostorg/test/issues/201  

When using `BOOST_TEST_DYN_LINK`, the linker flag `-lboost_unit_test_framework` is not sufficient to successfully compile. Apparently there are more libraries that must be linked. For instance, when linking only with `-lboost_unit_test_framework`, I get the error:  
  
```bash  
Undefined symbols for architecture x86_64:  
  "boost::unit_test::unit_test_log_t::instance()", referenced from:  
      ___cxx_global_var_init in lanczos_smoothing_test-05d504.o  
  "boost::unit_test::decorator::collector_t::instance()", referenced from:  
      ___cxx_global_var_init.3 in lanczos_smoothing_test-05d504.o  
  "boost::unit_test::ut_detail::auto_test_unit_registrar::auto_test_unit_registrar(boost::unit_test::test_case*, boost::unit_test::decorator::collector_t&, unsigned long)", referenced from:  
      ___cxx_global_var_init.3 in lanczos_smoothing_test-05d504.o  
ld: symbol(s) not found for architecture x86_64  
clang: fatal error: linker command failed with exit code 1 (use -v to see invocation)  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-02-01 19:31:30 UTC  
> Url: https://github.com/boostorg/test/issues/201#issuecomment-459840526  

Hi,  
  
Thanks for the report. This might be related to the various issues that popped out when the visibility defaults have been changed on the build system.  
Would it be possible for you to check if the branch `doc/various-updates-cleanups` is working as expected?  
  
Thanks!

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-02-01 20:27:54 UTC  
> Updated at: 2019-02-01 20:34:31 UTC  
> Url: https://github.com/boostorg/test/issues/201#issuecomment-459857207  

I checked out your suggested branch, compiled boost.test using the cmake file in `build/`. With the following makefile:  
  
```make  
CXX ?= g++  
CXXFLAGS := --std=gnu++17 -g -Wfatal-errors -Wall -Wextra -fvisibility=hidden  
SAN := -fsanitize=address -fsanitize=undefined  
LINKFLAGS := -L/usr/local/lib -pthread -L../test/build/  
INC := -I./include -I../function/include -I../type_index/include -I../bind/include -I../numeric/ublas/include -I../config/include -I../serialization/include -I../type_traits/include -I../mpl/include -I../preprocessor/include -I../static_assert/include -I../core/include -I../utility/include -I../typeof/include -I../assert/include -I../predef/include -I../lexical_cast/include -I../range/include -I../iterator/include -I../detail/include -I../concept_check/include -I../throw_exception/include -I../numeric/conversion/include -I../integer/include -I../array/include -I../container/include -I../move/include -I../multiprecision/include -I../container_hash/include -I../rational/include -I../smart_ptr/include -I../test/include -I/usr/local/include  
  
TEST_LINKFLAGS := -lboost_test_framework_shared  
  
all: mwe.x  
  
.PHONY: mwe.x  
test_mwe.x: external_main_example_2.cpp  
	$(CXX) $(CXXFLAGS) $(SAN) $(INC) $< -o $@ $(LINKFLAGS) $(TEST_LINKFLAGS)  
```  
  
I was able to successfully compile and link the example `test/example/external_main_example_2.cpp`.   
  
If I used the linker flag `-lboost_test_framework`, the example did not compile.  
  
Compiler: `Apple LLVM version 10.0.0 (clang-1000.11.45.5)`.  
  
The shared library is not documented on this branch:  
  
```bash  
test git:(doc/various-updates-cleanups) ✗ grep -nr 'boost_test_framework_shared' doc/  
# empty  
```

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-02-01 20:38:43 UTC  
> Updated at: 2019-02-01 20:40:23 UTC  
> Url: https://github.com/boostorg/test/issues/201#issuecomment-459860109  

Thanks for the quick feedback, you did a lot of work!  
  
The test `test/example/external_main_example_2.cpp` can work only for the shared version, so I would say this is normal. The CmakeLists.txt is for my own devs, so I am not expecting that it would work beyond simple use cases :) In particular, this is not mimicking the released version of boost.  
  
Now that you are on this particular branch, what about compiling boost.test and then compile the unit test module you reported as failing?  
  
I use this from the root folder (for OSX):  
```  
export current_version="1_70_0"  
export build_folder=boost_${current_version}  
  
./bootstrap.sh  
./b2 \  
  cflags="-mmacosx-version-min=10.10" \  
  cxxflags="-std=c++11 -stdlib=libc++ -mmacosx-version-min=10.10" \  
  mflags="-mmacosx-version-min=10.10" \  
  mmflags="-mmacosx-version-min=10.10" \  
  linkflags="-stdlib=libc++ -mmacosx-version-min=10.10" \  
  --prefix=`pwd`/../${build_folder} \  
  --without-log \  
  --layout=versioned \  
  install  
  
```  
  
Then, once compiled, you may try to recompile your own project againts boost.1.70 and report there.  
  
Thanks!  
  
EDIT: this is for OSX with C++11 support.   
EDIT2: If you know what you need as boost library (maybe only test), then you can pass `--with-libXXXX` as argument to `b2` to accelerate the compilation

---

## Comment 4

> Username: NAThompson  
> Created at: 2019-02-02 19:01:33 UTC  
> Url: https://github.com/boostorg/test/issues/201#issuecomment-459990287  

Hi,  
  
I have used your suggested syntax and successfully compiled, linked, and ran on top of your `doc/various-updates-cleanups` branch. So it appears that this issue has already been fixed, but the fixes didn't make it into 1.69? (Or maybe I'm still screwing up?) In any case, I'm satisfied with this outcome.

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2019-02-02 19:03:36 UTC  
> Url: https://github.com/boostorg/test/issues/201#issuecomment-459990439  

Thanks for the quick feedback. The changes will be there for 1.70 (merged this morning), there were many pending issues that couldn't solve in time for 1.69, sorry about that.  
  
Closing.

---

## Comment 6

> Username: NAThompson  
> Created at: 2019-02-02 19:08:58 UTC  
> Url: https://github.com/boostorg/test/issues/201#issuecomment-459990840  

Cool, thanks for your efforts. Looks like boost.test is really improving quickly!
