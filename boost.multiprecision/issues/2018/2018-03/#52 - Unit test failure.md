# #52 - Unit test failure [Closed]

> Username: NAThompson  
> Created at: 2018-03-29 10:11:22 UTC  
> Updated at: 2018-04-14 03:53:06 UTC  
> Closed at: 2018-04-14 03:53:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/52  

```  
testing.capture-output ../../../bin.v2/libs/multiprecision/test/test_move_mpfr.test/clang-darwin-4.2.1/release/test_move_mpfr.run  
====== BEGIN OUTPUT ======  
test_move.cpp(132): test 'allocation_count' failed in function 'int main()'  
1 error detected.  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
    DYLD_LIBRARY_PATH="/Users/nthompson/Documents/boost/bin.v2/libs/multiprecision/test/clang-darwin-4.2.1/release:$DYLD_LIBRARY_PATH"  
export DYLD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../bin.v2/libs/multiprecision/test/test_move_mpfr.test/clang-darwin-4.2.1/release/test_move_mpfr"   > "../../../bin.v2/libs/multiprecision/test/test_move_mpfr.test/clang-darwin-4.2.1/release/test_move_mpfr.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/multiprecision/test/test_move_mpfr.test/clang-darwin-4.2.1/release/test_move_mpfr.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/multiprecision/test/test_move_mpfr.test/clang-darwin-4.2.1/release/test_move_mpfr.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/multiprecision/test/test_move_mpfr.test/clang-darwin-4.2.1/release/test_move_mpfr.output" "../../../bin.v2/libs/multiprecision/test/test_move_mpfr.test/clang-darwin-4.2.1/release/test_move_mpfr.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/multiprecision/test/test_move_mpfr.test/clang-darwin-4.2.1/release/test_move_mpfr.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../bin.v2/libs/multiprecision/test/test_move_mpfr.test/clang-darwin-4.2.1/release/test_move_mpfr.run...  
```  
  
Config (if relevant):  
  
```  
➜  test git:(complex) ✗ ../../../b2 release --compiler=clang++ --toolset=clang  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - has_gmp builds           : yes  
    - has_gmp builds           : yes  
    - has_mpfr builds          : yes  
    - has_mpfr builds          : yes  
    - has_tommath builds       : no  
    - has_tommath builds       : no  
    - has_mpfi builds          : yes  
    - has_mpfi builds          : yes  
    - has_float128 builds      : no  
    - has_float128 builds      : no  
    - has_intel_quad builds    : no  
    - has_intel_quad builds    : no  
    - has_mpc builds           : yes  
    - has_mpc builds           : yes  
    - Boost.Config Feature Check: cxx11_constexpr : yes  
    - Boost.Config Feature Check: cxx11_user_defined_literals : yes  
    - Boost.Config Feature Check: cxx11_constexpr : yes (cached)  
    - Boost.Config Feature Check: cxx11_user_defined_literals : yes  
    - has_eigen builds         : no  
    - has_eigen builds         : no  
    - Boost.Config Feature Check: cxx11_lambdas : yes  
    - Boost.Config Feature Check: cxx11_lambdas : yes (cached)  
    - Boost.Config Feature Check: cxx11_nullptr : yes  
    - Boost.Config Feature Check: cxx11_nullptr : yes  
    - Boost.Config Feature Check: cxx11_numeric_limits : yes  
    - Boost.Config Feature Check: cxx11_numeric_limits : yes  
    - BOOST_COMP_GNUC >= 4.3.0 : no  (cached)  
    - Boost.Config Feature Check: cxx11_explicit_conversion_operators : yes  
    - Boost.Config Feature Check: cxx11_explicit_conversion_operators : yes  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-03-29 17:08:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/52#issuecomment-377305087  

That's weird - it means our custom allocation functions are never called.  
  
I'm unable to reproduce on Windows or Ubunutu, and the Travis OS X tests all passed too - are you able to debug locally?   It's a pretty trivial test case - the constructor at line 132:  
  
```  
      mpfr_float_50 a = 2;  
```  
  
should cause our custom allocator to be called.

---

## Comment 2

> Username: NAThompson  
> Created at: 2018-03-30 01:47:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/52#issuecomment-377422340  

Quick question: How do I run just one test from the unit test suite? My current method of running the tests always runs them all:  
  
```  
 test git:(complex) ✗ ../../../b2 release --compiler=clang++ --toolset=clang  
```

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-03-30 08:01:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/52#issuecomment-377472214  

Just add the name of the test to the end of the command line:  
  
```  
../../../b2 release --compiler=clang++ --toolset=clang test_move_mpfr  
```

---

## Comment 4

> Username: NAThompson  
> Created at: 2018-03-30 09:37:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/52#issuecomment-377489144  

Excellent. The only thing I was able to confirm was that neither   
  
```  
void *alloc_func(size_t n)  
{  
  
   ++allocation_count;  
   return (*alloc_func_ptr)(n);  
}  
```  
nor  
```  
void * realloc_func(void * p, size_t old, size_t n)  
{  
   ++allocation_count;  
   return (*realloc_func_ptr)(p, old, n);  
}  
```  
  
are called in my environment.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2018-03-30 11:03:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/52#issuecomment-377502507  

Looking at the mprf docs and source, it seems this behaviour may change depending on how mpfr and gmp are configured - basically `mp_set_memory_functions` has to be called before mpfr allocates any memory - that should be the case in that test program but there may be something going on in your particular configuration that we're not seeing elsewhere.  What's the mpfr version?

---

## Comment 6

> Username: NAThompson  
> Created at: 2018-03-30 14:32:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/52#issuecomment-377536784  

So in `test_move.cpp`, I did:  
  
```  
int main()  
{  
//#if defined(TEST_MPFR) || defined(TEST_GMP)  
//   mp_get_memory_functions(&alloc_func_ptr, &realloc_func_ptr, &free_func_ptr);  
//   mp_set_memory_functions(&alloc_func, &realloc_func, &free_func);  
//#endif  
```  
 and  
  
```  
int main()  
{  
//#if defined(TEST_MPFR) || defined(TEST_GMP)  
   mp_get_memory_functions(&alloc_func_ptr, &realloc_func_ptr, &free_func_ptr);  
   mp_set_memory_functions(&alloc_func, &realloc_func, &free_func);  
//#endif  
```  
  
just to make sure that nothing was being weird in the `#ifdef` conf. Both times it failed.  
  
mpfr version is 4.0.1.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2018-03-30 16:16:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/52#issuecomment-377560572  

OK, I'm still on mpfr-3.x and I bet most Linux packages are too.  I do note that they've changed the memory handling routines in mpfr-4.  Does adding `mpfr_mp_memory_cleanup();` before the call to `mp_set_memory_functions` fix things?  
  
BTW just installed mpfr-4 on Windows/Visual Studio and I'm unable to reproduce here.

---

## Comment 8

> Username: NAThompson  
> Created at: 2018-03-31 02:07:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/52#issuecomment-377658357  

Gave `mpfr_mp_memory_cleanup()` a shot, and the failure is still present.

---

## Comment 9

> Username: jzmaddock  
> Created at: 2018-03-31 17:23:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/52#issuecomment-377709214  

>Gave mpfr_mp_memory_cleanup() a shot, and the failure is still present.  
  
I've changed the check to disable the test if our custom allocators aren't called.  It's a <shrug> I guess.
