# #104 - stream_state_test.cpp failed in clang on Windows [Open]

> Username: yuxianch  
> Created at: 2019-11-29 02:17:03 UTC  
> Updated at: 2019-11-29 02:17:03 UTC  
> Url: https://github.com/boostorg/iostreams/issues/104  

I tried to use clang-cl on Windows(VS2019) to run the tests, and found [stream_state_test.cpp](https://github.com/boostorg/iostreams/blob/develop/test/stream_state_test.cpp) failed with the following error message.  
```  
ksh-3.2$ ./stream_state_test.exe  
Running 12 test cases...  
stream_state_test.cpp(89): error: in "stream state test/wrap_nothrow <&test_seekg>__execute": unexpected exception thrown by function(stream)  
stream_state_test.cpp(72): error: in "stream state test/wrap_nothrow <&test_seekg>__execute": stream still good  
stream_state_test.cpp(74): error: in "stream state test/wrap_nothrow <&test_seekg>__execute": stream did not set badbit  
stream_state_test.cpp(75): error: in "stream state test/wrap_nothrow <&test_seekg>__execute": stream did not fail  
stream_state_test.cpp(77): error: in "stream state test/wrap_nothrow <&test_seekg>__execute": stream does not report failure by operator !  
stream_state_test.cpp(79): error: in "stream state test/wrap_nothrow <&test_seekg>__execute": stream does not report failure by operator void* or bool  
stream_state_test.cpp(72): error: in "stream state test/wrap_throw <&test_seekg>__execute": stream still good  
stream_state_test.cpp(74): error: in "stream state test/wrap_throw <&test_seekg>__execute": stream did not set badbit  
stream_state_test.cpp(75): error: in "stream state test/wrap_throw <&test_seekg>__execute": stream did not fail  
stream_state_test.cpp(77): error: in "stream state test/wrap_throw <&test_seekg>__execute": stream does not report failure by operator !  
stream_state_test.cpp(79): error: in "stream state test/wrap_throw <&test_seekg>__execute": stream does not report failure by operator void* or bool  
unknown location(0): fatal error: in "stream state test/wrap_throw_delayed<&test_seekg>__execute": class std::ios_base::failure: bad seek: iostream stream error  
stream_state_test.cpp(89): error: in "stream state test/wrap_nothrow <&test_seekp>__execute": unexpected exception thrown by function(stream)  
stream_state_test.cpp(72): error: in "stream state test/wrap_nothrow <&test_seekp>__execute": stream still good  
stream_state_test.cpp(74): error: in "stream state test/wrap_nothrow <&test_seekp>__execute": stream did not set badbit  
stream_state_test.cpp(75): error: in "stream state test/wrap_nothrow <&test_seekp>__execute": stream did not fail  
stream_state_test.cpp(77): error: in "stream state test/wrap_nothrow <&test_seekp>__execute": stream does not report failure by operator !  
stream_state_test.cpp(79): error: in "stream state test/wrap_nothrow <&test_seekp>__execute": stream does not report failure by operator void* or bool  
stream_state_test.cpp(72): error: in "stream state test/wrap_throw <&test_seekp>__execute": stream still good  
stream_state_test.cpp(74): error: in "stream state test/wrap_throw <&test_seekp>__execute": stream did not set badbit  
stream_state_test.cpp(75): error: in "stream state test/wrap_throw <&test_seekp>__execute": stream did not fail  
stream_state_test.cpp(77): error: in "stream state test/wrap_throw <&test_seekp>__execute": stream does not report failure by operator !  
stream_state_test.cpp(79): error: in "stream state test/wrap_throw <&test_seekp>__execute": stream does not report failure by operator void* or bool  
unknown location(0): fatal error: in "stream state test/wrap_throw_delayed<&test_seekp>__execute": class std::ios_base::failure: bad seek: iostream stream error  
  
*** 24 failures are detected in the test module "Master Test Suite"  
```  
Seems that clang on Windows does not behave properly on the wrap_nothrow, wrap_throw and throw_delayed cases.
