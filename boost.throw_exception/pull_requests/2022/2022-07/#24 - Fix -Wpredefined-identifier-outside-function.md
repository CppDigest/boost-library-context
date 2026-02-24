# #24 Fix -Wpredefined-identifier-outside-function [Closed]

> Username: mborland  
> Created at: 2022-07-16 19:24:00 UTC  
> Updated at: 2022-08-04 01:40:12 UTC  
> Closed at: 2022-08-04 01:40:12 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/24  

Also update the gitignore to work with vscode-cmake toolchains.

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-07-16 20:36:03 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/24#issuecomment-1186286247  

Unless I'm missing something, this changes the source location to point inside throw_with_location rather than inside the caller, which makes it useless?

---

## Comment 2

> Username: mborland  
> Created_at: 2022-07-17 00:25:04 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/24#issuecomment-1186350177  

> Unless I'm missing something, this changes the source location to point inside throw_with_location rather than inside the caller, which makes it useless?  
  
When used as a default argument in the function declaration won't `__PRETTY_FUNCTION__` expand to `throw_with_location` instead of the caller location? That would be equally unhelpful.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-07-17 07:40:24 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/24#issuecomment-1186432009  

https://www.boost.org/doc/libs/1_79_0/libs/throw_exception/doc/html/throw_exception.html#using_boostthrow_with_location  
https://godbolt.org/z/5vj8eGEeT

---

## Comment 4

> Username: pdimov  
> Created_at: 2022-07-17 08:28:07 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/24#issuecomment-1186444340  

How are you getting this warning? I don't see it with -Wall -Wextra on the above example using GCC 5 or 6, which are the only ones where `__PRETTY_FUNCTION__` is used.

---

## Comment 5

> Username: mborland  
> Created_at: 2022-07-19 16:09:02 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/24#issuecomment-1189263111  

> How are you getting this warning? I don't see it with -Wall -Wextra on the above example using GCC 5 or 6, which are the only ones where `__PRETTY_FUNCTION__` is used.  
  
Using Apple Clang 13.1.6 targeting ARM64. On develop I also get the below which may be symptomatic of an issue supporting this target:  
  
```  
testing.capture-output ../../../bin.v2/libs/throw_exception/test/throw_with_location_test3.test/darwin-13.1.6/debug/cxxstd-20-iso/threading-multi/visibility-hidden/throw_with_location_test3.run  
====== BEGIN OUTPUT ======  
throw_with_location_test3.cpp(26): test 'loc.file_name() == ""' ('(unknown)' == '') failed in function 'int main()'  
throw_with_location_test3.cpp(27): test 'loc.function_name() == ""' ('(unknown)' == '') failed in function 'int main()'  
2 errors detected.  
  
EXIT STATUS: 2  
====== END OUTPUT ======  
  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../bin.v2/libs/throw_exception/test/throw_with_location_test3.test/darwin-13.1.6/debug/cxxstd-20-iso/threading-multi/visibility-hidden/throw_with_location_test3"   > "../../../bin.v2/libs/throw_exception/test/throw_with_location_test3.test/darwin-13.1.6/debug/cxxstd-20-iso/threading-multi/visibility-hidden/throw_with_location_test3.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/throw_exception/test/throw_with_location_test3.test/darwin-13.1.6/debug/cxxstd-20-iso/threading-multi/visibility-hidden/throw_with_location_test3.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/throw_exception/test/throw_with_location_test3.test/darwin-13.1.6/debug/cxxstd-20-iso/threading-multi/visibility-hidden/throw_with_location_test3.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/throw_exception/test/throw_with_location_test3.test/darwin-13.1.6/debug/cxxstd-20-iso/threading-multi/visibility-hidden/throw_with_location_test3.output" "../../../bin.v2/libs/throw_exception/test/throw_with_location_test3.test/darwin-13.1.6/debug/cxxstd-20-iso/threading-multi/visibility-hidden/throw_with_location_test3.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/throw_exception/test/throw_with_location_test3.test/darwin-13.1.6/debug/cxxstd-20-iso/threading-multi/visibility-hidden/throw_with_location_test3.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../bin.v2/libs/throw_exception/test/throw_with_location_test3.test/darwin-13.1.6/debug/cxxstd-20-iso/threading-multi/visibility-hidden/throw_with_location_test3.run...  
```

---

## Comment 6

> Username: pdimov  
> Created_at: 2022-07-20 16:42:27 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/24#issuecomment-1190511054  

Something isn't right here. Are you sure that your Boost installation is good? That is, are you sure that Assert and ThrowException are the same version? This looks like the old BOOST_CURRENT_LOCATION from 1.78.

---

## Comment 7

> Username: mborland  
> Created_at: 2022-07-21 13:15:41 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/24#issuecomment-1191472270  

> Something isn't right here. Are you sure that your Boost installation is good? That is, are you sure that Assert and ThrowException are the same version? This looks like the old BOOST_CURRENT_LOCATION from 1.78.  
  
You are correct. I pulled the 1.80 beta branch but still had 1.78 check out. With 1.80 I do not see this warning anymore but likely unrelated I get:  
  
```  
 ../../../b2 cxxstd=17 cxxflags="-Wall -Wextra -Wpedantic"                                                                                                                            ─╯  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : arm (cached) [1]  
    - symlinks supported       : yes (cached)  
  
[1] darwin-13.1.6  
...patience...  
...found 916 targets...  
...updating 6 targets...  
testing.capture-output ../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test2.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test2.run  
/bin/sh: line 7: 95313 Abort trap: 6           "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test2.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test2" > "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test2.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test2.output" 2>&1 < /dev/null  
====== BEGIN OUTPUT ======  
libc++abi: terminating with uncaught exception of type boost::wrapexcept<my_exception>: std::exception  
  
EXIT STATUS: 134  
====== END OUTPUT ======  
  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test2.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test2"   > "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test2.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test2.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test2.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test2.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test2.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test2.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test2.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test2.output" "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test2.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test2.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test2.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test2.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test2.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test2.run...  
testing.capture-output ../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test.run  
/bin/sh: line 7: 95312 Abort trap: 6           "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test" > "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test.output" 2>&1 < /dev/null  
====== BEGIN OUTPUT ======  
libc++abi: terminating with uncaught exception of type boost::wrapexcept<my_exception>: std::exception  
  
EXIT STATUS: 134  
====== END OUTPUT ======  
  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test"   > "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test.output" "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../bin.v2/libs/throw_exception/test/throw_exception_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_exception_nx_test.run...  
testing.capture-output ../../../bin.v2/libs/throw_exception/test/throw_with_location_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_with_location_nx_test.run  
/bin/sh: line 7: 95314 Abort trap: 6           "../../../bin.v2/libs/throw_exception/test/throw_with_location_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_with_location_nx_test" > "../../../bin.v2/libs/throw_exception/test/throw_with_location_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_with_location_nx_test.output" 2>&1 < /dev/null  
====== BEGIN OUTPUT ======  
libc++abi: terminating with uncaught exception of type boost::detail::with_throw_location<my_exception>: std::exception  
  
EXIT STATUS: 134  
====== END OUTPUT ======  
  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../bin.v2/libs/throw_exception/test/throw_with_location_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_with_location_nx_test"   > "../../../bin.v2/libs/throw_exception/test/throw_with_location_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_with_location_nx_test.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/throw_exception/test/throw_with_location_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_with_location_nx_test.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/throw_exception/test/throw_with_location_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_with_location_nx_test.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/throw_exception/test/throw_with_location_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_with_location_nx_test.output" "../../../bin.v2/libs/throw_exception/test/throw_with_location_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_with_location_nx_test.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/throw_exception/test/throw_with_location_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_with_location_nx_test.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../bin.v2/libs/throw_exception/test/throw_with_location_nx_test.test/darwin-13.1.6/debug/cxxstd-17-iso/exception-handling-off/threading-multi/visibility-hidden/throw_with_location_nx_test.run...  
...failed updating 3 targets...  
...skipped 3 targets...  
```  
  
This occurs with Clang 13.1.6 but not with GCC 12.1.0.

---

## Comment 8

> Username: pdimov  
> Created_at: 2022-07-21 15:32:04 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/24#issuecomment-1191635841  

This indicates some other configuration problem, because the nx tests test `-fno-exceptions` (there's an `exception-handling-off` portion in the path so the b2 feature is at least set correctly, but it's not clear whether `-fno-exceptions` is correctly passed.)  
  
One thing that jumps at me is that you're using `toolset=darwin`, which means GCC (from the time Apple still used GCC - this toolset is probably long obsolete now.) You should be using `toolset=clang`.

---
