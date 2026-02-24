# #451 - undefined-sanitizer, xenial on Travis CI [Open]

> Username: jeking3  
> Created at: 2019-06-10 14:44:41 UTC  
> Updated at: 2021-06-11 01:55:42 UTC  
> Url: https://github.com/boostorg/build/issues/451  

On Travis CI using the xenial image, when compiling boostorg/uuid with `undefined-sanitizer=norecover` I get a link error:  
  
https://travis-ci.org/boostorg/uuid/jobs/543694135  
```  
b2 toolset=gcc-8 cxxstd=03,11,14 undefined-sanitizer=norecover define=BOOST_NO_STRESS_TEST=1 variant=debug -j3  
...  
gcc.link ../../bin.v2/libs/uuid/test/test_include1.test/gcc-8/debug/cxxstd-03-iso/link-static/threading-multi/undefined-sanitizer-norecover/visibility-hidden/test_include1  
/usr/bin/ld: unrecognized option '--push-state--no-as-needed'  
/usr/bin/ld: use the --help option for usage information  
collect2: error: ld returned 1 exit status  
    "g++-8"    -o "../../bin.v2/libs/uuid/test/test_include1.test/gcc-8/debug/cxxstd-03-iso/link-static/threading-multi/undefined-sanitizer-norecover/visibility-hidden/test_include1" -Wl,--start-group "../../bin.v2/libs/uuid/test/test_include1.test/gcc-8/debug/cxxstd-03-iso/link-static/threading-multi/undefined-sanitizer-norecover/visibility-hidden/test_include1.o" "../../bin.v2/libs/uuid/test/test_include1.test/gcc-8/debug/cxxstd-03-iso/link-static/threading-multi/undefined-sanitizer-norecover/visibility-hidden/test_include2.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -m64 -pthread -std=c++03 -g -fvisibility=hidden -fvisibility-inlines-hidden -fsanitize=undefined -fno-sanitize-recover=undefined   
...failed gcc.link ../../bin.v2/libs/uuid/test/test_include1.test/gcc-8/debug/cxxstd-03-iso/link-static/threading-multi/undefined-sanitizer-norecover/visibility-hidden/test_include1...  
```  
  
A workaround to this is to force the use of the gold linker:  
  
https://travis-ci.org/boostorg/uuid/jobs/543759905  
```  
b2 toolset=gcc-8 cxxstd=03,11,14 undefined-sanitizer=norecover define=BOOST_NO_STRESS_TEST=1 linkflags=-fuse-ld=gold variant=debug -j3  
```  
  
It looks like a linker option is being used that the standard `ld` linker does not understand?

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:04 UTC  
> Url: https://github.com/boostorg/build/issues/451#issuecomment-859203352  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
