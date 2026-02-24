# #21 - Example build failure with gcc 5.4, cxxstd=11 [Closed]

> Username: jeking3  
> Created at: 2019-06-03 20:25:42 UTC  
> Updated at: 2019-06-04 12:29:35 UTC  
> Closed at: 2019-06-03 20:35:44 UTC  
> Url: https://github.com/boostorg/contract/issues/21  

This is with the tip of develop as of this morning:  
  
``b2 toolset=gcc-5 cxxstd=11 variant=debug -j3``  
  
```  
testing.capture-output ../../bin.v2/libs/contract/example/features-union.test/gcc-5/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.run  
====== BEGIN OUTPUT ======  
postcondition assertion "!integer()" failed: file "../../libs/contract/example/features/union.cpp", line 100  
terminate called after throwing an instance of 'boost::contract::assertion_failure'  
  what():  assertion "!integer()" failed: file "../../libs/contract/example/features/union.cpp", line 100  
Aborted (core dumped)  
  
EXIT STATUS: 134  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/home/travis/build/jeking3/boost-root/bin.v2/libs/contract/build/gcc-5/debug/cxxstd-11-iso/threading-multi/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/system/build/gcc-5/debug/cxxstd-11-iso/threading-multi/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/contract/example/features-union.test/gcc-5/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union"   > "../../bin.v2/libs/contract/example/features-union.test/gcc-5/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/contract/example/features-union.test/gcc-5/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/contract/example/features-union.test/gcc-5/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/contract/example/features-union.test/gcc-5/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.output" "../../bin.v2/libs/contract/example/features-union.test/gcc-5/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/contract/example/features-union.test/gcc-5/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../bin.v2/libs/contract/example/features-union.test/gcc-5/debug/cxxstd-11-iso/threading-multi/visibility-hidden/features-union.run...  
```  
  
https://api.travis-ci.org/v3/job/540752932/log.txt

---

## Comment 1

> Username: lcaminiti  
> Created at: 2019-06-03 20:35:44 UTC  
> Url: https://github.com/boostorg/contract/issues/21#issuecomment-498416475  

Yes, I saw this yesterday and fixed it 4h ago. If you update develop it should be fixed now. Unfortunately, I don't know if the fix re-introduced a valgrind warning that was there a few months back...  
  
In any case, those are examples, not tests, so you don't need to run them. I won't fixed the valgrind warning even if it's re-introduced now because it's specific to an example used in the docs and it doesn't matter for the lib and its tests.

---

## Comment 2

> Username: jeking3  
> Created at: 2019-06-04 10:20:55 UTC  
> Url: https://github.com/boostorg/contract/issues/21#issuecomment-498613042  

Examples should work however, otherwise they are bad examples.

---

## Comment 3

> Username: jeking3  
> Created at: 2019-06-04 12:29:35 UTC  
> Url: https://github.com/boostorg/contract/issues/21#issuecomment-498650640  

Yes, this reintroduced the valgrind issue.  Having CI on the repository will help eliminate this game of whack-a-mole.  I'm working on it.
