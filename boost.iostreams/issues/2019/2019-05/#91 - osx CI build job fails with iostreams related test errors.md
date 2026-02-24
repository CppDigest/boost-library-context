# #91 - osx CI build job fails with iostreams related test errors [Open]

> Username: jeking3  
> Created at: 2019-05-02 11:21:05 UTC  
> Updated at: 2019-05-02 11:21:05 UTC  
> Url: https://github.com/boostorg/iostreams/issues/91  

This job was newly added.  Looks like there are some issues to clean up.  Making it an allowed failure for now.  
  
Failing build example:  
  
https://travis-ci.org/boostorg/iostreams/jobs/525323051  
  
Failing test output:  
  
```  
testing.capture-output ../../bin.v2/libs/iostreams/test/stream_state_test.test/clang-darwin-9.1/release/cxxstd-03-iso/threading-multi/visibility-hidden/stream_state_test.run  
====== BEGIN OUTPUT ======  
Running 12 test cases...  
../../libs/iostreams/test/stream_state_test.cpp:89: error: in "stream state test/wrap_nothrow <&test_seekp>__execute": unexpected exception thrown by function(stream)  
../../libs/iostreams/test/stream_state_test.cpp:72: error: in "stream state test/wrap_nothrow <&test_seekp>__execute": stream still good  
../../libs/iostreams/test/stream_state_test.cpp:74: error: in "stream state test/wrap_nothrow <&test_seekp>__execute": stream did not set badbit  
../../libs/iostreams/test/stream_state_test.cpp:75: error: in "stream state test/wrap_nothrow <&test_seekp>__execute": stream did not fail  
../../libs/iostreams/test/stream_state_test.cpp:77: error: in "stream state test/wrap_nothrow <&test_seekp>__execute": stream does not report failure by operator !  
../../libs/iostreams/test/stream_state_test.cpp:79: error: in "stream state test/wrap_nothrow <&test_seekp>__execute": stream does not report failure by operator void* or bool  
../../libs/iostreams/test/stream_state_test.cpp:72: error: in "stream state test/wrap_throw <&test_seekp>__execute": stream still good  
../../libs/iostreams/test/stream_state_test.cpp:74: error: in "stream state test/wrap_throw <&test_seekp>__execute": stream did not set badbit  
../../libs/iostreams/test/stream_state_test.cpp:75: error: in "stream state test/wrap_throw <&test_seekp>__execute": stream did not fail  
../../libs/iostreams/test/stream_state_test.cpp:77: error: in "stream state test/wrap_throw <&test_seekp>__execute": stream does not report failure by operator !  
../../libs/iostreams/test/stream_state_test.cpp:79: error: in "stream state test/wrap_throw <&test_seekp>__execute": stream does not report failure by operator void* or bool  
unknown location:0: fatal error: in "stream state test/wrap_throw_delayed<&test_seekp>__execute": std::__1::ios_base::failure: bad seek: unspecified iostream_category error  
*** 12 failures are detected in the test module "Master Test Suite"  
EXIT STATUS: 201  
====== END OUTPUT ======  
      
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/iostreams/test/stream_state_test.test/clang-darwin-9.1/release/cxxstd-03-iso/threading-multi/visibility-hidden/stream_state_test"   > "../../bin.v2/libs/iostreams/test/stream_state_test.test/clang-darwin-9.1/release/cxxstd-03-iso/threading-multi/visibility-hidden/stream_state_test.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/iostreams/test/stream_state_test.test/clang-darwin-9.1/release/cxxstd-03-iso/threading-multi/visibility-hidden/stream_state_test.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/iostreams/test/stream_state_test.test/clang-darwin-9.1/release/cxxstd-03-iso/threading-multi/visibility-hidden/stream_state_test.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/iostreams/test/stream_state_test.test/clang-darwin-9.1/release/cxxstd-03-iso/threading-multi/visibility-hidden/stream_state_test.output" "../../bin.v2/libs/iostreams/test/stream_state_test.test/clang-darwin-9.1/release/cxxstd-03-iso/threading-multi/visibility-hidden/stream_state_test.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/iostreams/test/stream_state_test.test/clang-darwin-9.1/release/cxxstd-03-iso/threading-multi/visibility-hidden/stream_state_test.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
...failed testing.capture-output ../../bin.v2/libs/iostreams/test/stream_state_test.test/clang-darwin-9.1/release/cxxstd-03-iso/threading-multi/visibility-hidden/stream_state_test.run...  
```
