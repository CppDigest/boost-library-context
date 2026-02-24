# #34 - run-boost_assert-source_location_test* test failures in 1.83.0 with Intel LLVM compiler [Closed]

> Username: iskunk  
> Created at: 2023-11-28 03:43:16 UTC  
> Updated at: 2023-11-29 04:41:57 UTC  
> Closed at: 2023-11-29 04:41:57 UTC  
> Url: https://github.com/boostorg/assert/issues/34  

I am building Boost 1.83.0 with CMake and the Intel LLVM compiler (version 2024.0.0).  
  
Everything builds fine, but I get the following test failures:  
```  
The following tests FAILED:  
	 13 - run-boost_assert-source_location_test (Failed)  
	 15 - run-boost_assert-source_location_test3 (Failed)  
	 16 - run-boost_assert-source_location_test4 (Failed)  
	1480 - run-boost_throw_exception-throw_exception_test4 (Failed)  
```  
  
(The last failure appears unrelated, but the log will show otherwise.)  
  
Note that I had previously compiled this same version of Boost with an earlier version of Intel LLVM, and did not get these failures then, so there may be something in the behavior of the new compiler that is at fault.  
  
Below are the test outputs. Note that the source tree is in `/home/src/boost/1.83.0/`, and the build tree is at `/tmp/boost-1.83.0-work/build`.  
  
  
### run-boost_assert-source_location_test  
```  
13/1661 Testing: run-boost_assert-source_location_test  
13/1661 Test: run-boost_assert-source_location_test  
Command: "/tmp/boost-1.83.0-work/build/stage/bin/boost_assert-source_location_test"  
Directory: /tmp/boost-1.83.0-work/build/libs/assert/test  
"run-boost_assert-source_location_test" start time: Nov 28 02:37 UTC  
Output:  
----------------------------------------------------------  
/home/src/boost/1.83.0/libs/assert/test/source_location_test.cpp(40): test 'loc.file_name() == __FILE__' ('source_location_test.cpp' == '/home/src/boost/1.83.0/libs/assert/test/source_location_test.cpp') failed in function 'int main()'  
/home/src/boost/1.83.0/libs/assert/test/source_location_test.cpp(47): test 'loc.file_name() == __FILE__' ('source_location_test.cpp' == '/home/src/boost/1.83.0/libs/assert/test/source_location_test.cpp') failed in function 'int main()'  
2 errors detected.  
<end of output>  
Test time =   0.00 sec  
----------------------------------------------------------  
Test Failed.  
"run-boost_assert-source_location_test" end time: Nov 28 02:37 UTC  
"run-boost_assert-source_location_test" time elapsed: 00:00:00  
```  
  
### run-boost_assert-source_location_test3  
```  
15/1661 Testing: run-boost_assert-source_location_test3  
15/1661 Test: run-boost_assert-source_location_test3  
Command: "/tmp/boost-1.83.0-work/build/stage/bin/boost_assert-source_location_test3"  
Directory: /tmp/boost-1.83.0-work/build/libs/assert/test  
"run-boost_assert-source_location_test3" start time: Nov 28 02:37 UTC  
Output:  
----------------------------------------------------------  
/home/src/boost/1.83.0/libs/assert/test/source_location_test3.cpp(65): test 'loc.to_string().substr( 0, prefix.size() ) == prefix' ('source_location_test3.cpp:62:38 in function 'main'' == '/home/src/boost/1.83.0/libs/assert/test/source_location_test3.cpp:62') failed in function 'int main()'  
1 error detected.  
<end of output>  
Test time =   0.00 sec  
----------------------------------------------------------  
Test Failed.  
"run-boost_assert-source_location_test3" end time: Nov 28 02:37 UTC  
"run-boost_assert-source_location_test3" time elapsed: 00:00:00  
```  
  
### run-boost_assert-source_location_test4  
```  
6/1661 Testing: run-boost_assert-source_location_test4  
16/1661 Test: run-boost_assert-source_location_test4  
Command: "/tmp/boost-1.83.0-work/build/stage/bin/boost_assert-source_location_test4"  
Directory: /tmp/boost-1.83.0-work/build/libs/assert/test  
"run-boost_assert-source_location_test4" start time: Nov 28 02:37 UTC  
Output:  
----------------------------------------------------------  
/home/src/boost/1.83.0/libs/assert/test/source_location_test4.cpp(21): test 's_loc.file_name() == __FILE__' ('source_location_test4.cpp' == '/home/src/boost/1.83.0/libs/assert/test/source_location_test4.cpp') failed in function 'int main()'  
/home/src/boost/1.83.0/libs/assert/test/source_location_test4.cpp(32): test 'c_loc.file_name() == __FILE__' ('source_location_test4.cpp' == '/home/src/boost/1.83.0/libs/assert/test/source_location_test4.cpp') failed in function 'int main()'  
/home/src/boost/1.83.0/libs/assert/test/source_location_test4.cpp(39): test 'loc.file_name() == __FILE__' ('source_location_test4.cpp' == '/home/src/boost/1.83.0/libs/assert/test/source_location_test4.cpp') failed in function 'int main()'  
3 errors detected.  
<end of output>  
Test time =   0.00 sec  
----------------------------------------------------------  
Test Failed.  
"run-boost_assert-source_location_test4" end time: Nov 28 02:37 UTC  
"run-boost_assert-source_location_test4" time elapsed: 00:00:00  
```  
  
### run-boost_throw_exception-throw_exception_test4  
```  
1480/1661 Testing: run-boost_throw_exception-throw_exception_test4  
1480/1661 Test: run-boost_throw_exception-throw_exception_test4  
Command: "/tmp/boost-1.83.0-work/build/stage/bin/boost_throw_exception-throw_exception_test4"  
Directory: /tmp/boost-1.83.0-work/build/libs/throw_exception/test  
"run-boost_throw_exception-throw_exception_test4" start time: Nov 28 02:44 UTC  
Output:  
----------------------------------------------------------  
/home/src/boost/1.83.0/libs/throw_exception/test/throw_exception_test4.cpp(43): test '*file == __FILE__' ('throw_exception_test4.cpp' == '/home/src/boost/1.83.0/libs/throw_exception/test/throw_exception_test4.cpp') failed in function 'int main()'  
/home/src/boost/1.83.0/libs/throw_exception/test/throw_exception_test4.cpp(71): test '*file == __FILE__' ('throw_exception_test4.cpp' == '/home/src/boost/1.83.0/libs/throw_exception/test/throw_exception_test4.cpp') failed in function 'int main()'  
/home/src/boost/1.83.0/libs/throw_exception/test/throw_exception_test4.cpp(99): test '*file == __FILE__' ('throw_exception_test4.cpp' == '/home/src/boost/1.83.0/libs/throw_exception/test/throw_exception_test4.cpp') failed in function 'int main()'  
3 errors detected.  
<end of output>  
Test time =   0.00 sec  
----------------------------------------------------------  
Test Failed.  
"run-boost_throw_exception-throw_exception_test4" end time: Nov 28 02:44 UTC  
"run-boost_throw_exception-throw_exception_test4" time elapsed: 00:00:00  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2023-11-28 09:07:13 UTC  
> Url: https://github.com/boostorg/assert/issues/34#issuecomment-1829386200  

Looks like icx starting with version 2021.3.0 strips the path from `__FILE__`, leaving only the filename, in `std::source_location`, so the tests should be fixed to account for that. I'll look into it.

---

## Comment 2

> Username: pdimov  
> Created at: 2023-11-29 00:07:20 UTC  
> Url: https://github.com/boostorg/assert/issues/34#issuecomment-1830975649  

The develop branch should be fixed now, via https://github.com/boostorg/assert/commit/8919429f41799cdcf37dcf7c2836379d287a809e, https://github.com/boostorg/assert/commit/2934b3ffb8d3e198db9a4a0cce149c856103040c and https://github.com/boostorg/assert/commit/9067f0987bb952d8ff266529d03e39aa1af3ffee. I'll take care of the ThrowException test next.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-11-29 04:41:57 UTC  
> Url: https://github.com/boostorg/assert/issues/34#issuecomment-1831206911  

Should be fixed on master as well, and in 1.84.
