# #14 - Build failure with gcc 4.8 and gcc 4.9, cxxstd=11 [Closed]

> Username: jeking3  
> Created at: 2018-11-04 22:21:41 UTC  
> Updated at: 2019-06-04 14:44:08 UTC  
> Closed at: 2019-06-02 16:36:54 UTC  
> Url: https://github.com/boostorg/contract/issues/14  

https://api.travis-ci.org/v3/job/450610131/log.txt  
```  
testing.capture-output ../../bin.v2/libs/contract/test/public_function-throwing_body_virtual.test/gcc-4.8/release/cxxstd-11-iso/visibility-hidden/public_function-throwing_body_virtual.run  
====== BEGIN OUTPUT ======  
d::static_inv  
d::inv  
e::static_inv  
e::inv  
c::static_inv  
c::inv  
a::static_inv  
a::inv  
d::f::pre  
d::f::old  
e::f::old  
c::f::old  
a::f::old  
a::f::body  
d::static_inv  
d::inv  
e::static_inv  
e::inv  
c::static_inv  
c::inv  
a::static_inv  
a::inv  
d::f::old  
d::f::except  
e::f::old  
e::f::except  
c::f::old  
c::f::except  
a::f::except  
terminate called after throwing an instance of 'except_error'  
Aborted (core dumped)  
  
EXIT STATUS: 134  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/home/travis/build/jeking3/boost-root/bin.v2/libs/contract/build/gcc-4.8/release/cxxstd-11-iso/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/system/build/gcc-4.8/release/cxxstd-11-iso/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/contract/test/public_function-throwing_body_virtual.test/gcc-4.8/release/cxxstd-11-iso/visibility-hidden/public_function-throwing_body_virtual"   > "../../bin.v2/libs/contract/test/public_function-throwing_body_virtual.test/gcc-4.8/release/cxxstd-11-iso/visibility-hidden/public_function-throwing_body_virtual.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/contract/test/public_function-throwing_body_virtual.test/gcc-4.8/release/cxxstd-11-iso/visibility-hidden/public_function-throwing_body_virtual.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/contract/test/public_function-throwing_body_virtual.test/gcc-4.8/release/cxxstd-11-iso/visibility-hidden/public_function-throwing_body_virtual.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/contract/test/public_function-throwing_body_virtual.test/gcc-4.8/release/cxxstd-11-iso/visibility-hidden/public_function-throwing_body_virtual.output" "../../bin.v2/libs/contract/test/public_function-throwing_body_virtual.test/gcc-4.8/release/cxxstd-11-iso/visibility-hidden/public_function-throwing_body_virtual.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/contract/test/public_function-throwing_body_virtual.test/gcc-4.8/release/cxxstd-11-iso/visibility-hidden/public_function-throwing_body_virtual.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../bin.v2/libs/contract/test/public_function-throwing_body_virtual.test/gcc-4.8/release/cxxstd-11-iso/visibility-hidden/public_function-throwing_body_virtual.run...  
```  
(Note that gcc-5 passes)

---

## Comment 1

> Username: lcaminiti  
> Created at: 2018-12-22 14:21:18 UTC  
> Updated at: 2018-12-22 14:24:34 UTC  
> Url: https://github.com/boostorg/contract/issues/14#issuecomment-449573807  

I've seen this failure on GCC 4.7.3 before: This test fails because `std::unchaught_exception` seems to always return zero on this compiler (even if the authors could not find a direct reference to this possible compiler issue online).  
In any case, looking at the regression tests, they all pass on GCC 4.8 teeks99-02-mg4.8-Docker-64on64 but they all fails on GCC 4.8 igaztanaga-master-gcc-4.8c++11. I will investigate the reason of all these test failures on igaztanaga-master-gcc-4.8c++11 (those include this issue as well).

---

## Comment 2

> Username: lcaminiti  
> Created at: 2019-06-02 16:36:54 UTC  
> Url: https://github.com/boostorg/contract/issues/14#issuecomment-498046306  

Marked some of these as expected failures with relative comment, also cleaned up some warnings.

---

## Comment 3

> Username: jeking3  
> Created at: 2019-06-04 11:19:47 UTC  
> Url: https://github.com/boostorg/contract/issues/14#issuecomment-498629863  

Still seeing this failure with a sync to develop as of this morning.  If they were marked as expected failures in the test suite then this should not have caused a build failure.  If it was by other means, let me know...  
  
https://api.travis-ci.org/v3/job/541174207/log.txt  
  
Search for "ailed " and you will find it...

---

## Comment 4

> Username: jeking3  
> Created at: 2019-06-04 11:20:42 UTC  
> Url: https://github.com/boostorg/contract/issues/14#issuecomment-498630171  

Also failing on gcc-4.9:  
  
https://api.travis-ci.org/v3/job/541174208/log.txt  
  
Search for "ailed " and you will find it...

---

## Comment 5

> Username: jeking3  
> Created at: 2019-06-04 14:44:08 UTC  
> Url: https://github.com/boostorg/contract/issues/14#issuecomment-498702782  

Okay, the expected failures you're managing are for the test matrix, now I understand.  So it looks like gcc 4.8 and 4.9 are expected failures.  I have marked them as such in the upcoming pull request for continuous integration.
