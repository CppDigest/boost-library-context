# #682 - ppc64le failing tests with IEEE128 [Closed]

> Username: mscastanho  
> Created at: 2021-08-30 17:21:13 UTC  
> Updated at: 2023-04-06 12:18:16 UTC  
> Closed at: 2023-04-06 12:18:16 UTC  
> Url: https://github.com/boostorg/math/issues/682  

I've been testing boost Math compiled with GCC 12 (dev) using IEEE long double on ppc64le (instead of IBM long double format, the default). There are quite a few tests failing because the maximum error is higher than the tolerance used, most cases are just slightly off, others have errors a few orders of magnitude larger than the tolerance. I'd be happy to provide more info if needed.  
  
Besides these, there is one test that is failing at the compilation step: `cstdfloat_cmath_incl_test`   
```  
compile_test/cstdfloat_cmath_incl_test.cpp: In function ‘void compile_and_link_test()’:  
compile_test/cstdfloat_cmath_incl_test.cpp:56:42: error: ‘nanq’ is not a member of ‘std’; did you mean ‘nanl’?  
   56 |     check_result<boost::float128_t>(std::nanq(0));  
      |                                          ^~~~  
      |                                          nanl  
...failed updating 1 target...  
```  
  
On ppc64le, when GCC is compiled with `--with-long-double-128 --with-long-double-format=ieee`, `__float128` becomes an alias to `long double`. As a consequence, libstdc++ does not define `_GLIBCXX_USE_FLOAT128`  anymore (see gcc-mirror/gcc@7c1e7eed891a7) , and so neither  `BOOST_HAS_FLOAT128` nor `BOOST_MATH_USE_FLOAT128` are defined. For this reason, `nanq` is not being defined in `cstdfloat_cmath.hpp`.  
  
Do you have any pointers on how I could solve these issues? I'm still trying to find my way around the codebase.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-08-30 17:52:29 UTC  
> Updated at: 2021-08-30 17:52:37 UTC  
> Url: https://github.com/boostorg/math/issues/682#issuecomment-908556499  

> have any pointers on how I could solve these issues?  
  
At the moment no. But...  
  
> I'm still trying to find my way around the codebase.  
  
This is a good time to look into (or try to solve) this since we are actually working on the general area of `<cstdfloat>` at the moment in #506.  
  
I do not know how to get that compiler. Any suggestions?

---

## Comment 2

> Username: mscastanho  
> Created at: 2021-08-30 19:12:18 UTC  
> Url: https://github.com/boostorg/math/issues/682#issuecomment-908614153  

@ckormanyos The issue can be reproduced with a GCC built from latest source with the proper flags to make ieee128 the default long double format. But you will likely need a ppc64le machine to run the tests.  
```  
$ git clone git://gcc.gnu.org/git/gcc.git ~/src/gcc  
  
$ mkdir -p ~/build/gcc  
$ cd ~/build/gcc  
  
$  ~/src/gcc/configure --prefix=/home/mscastanho/usr --with-long-double-128 --with-long-double-format=ieee --build=powerpc64le-linux-gnu --host=powerpc64le-linux-gnu --target=powerpc64le-linux-gnu --enable-languages=c,c++ --disable-bootstrap  
  
$ make -j20  
$ make install  
```

---

## Comment 3

> Username: ckormanyos  
> Created at: 2021-08-30 19:18:19 UTC  
> Url: https://github.com/boostorg/math/issues/682#issuecomment-908618832  

> issue can be reproduced with a GCC built from latest source  
  
That's one I have not done in a while. I would (if i get a chance) build on Linux host for that `--target`. Haven't done a cross GCC build in a while. That interests me cause I'd like to have that cross compiler even if I don't have the target machine.  
  
Thanks @mscastanho for the source and flags.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-08-31 18:28:00 UTC  
> Url: https://github.com/boostorg/math/issues/682#issuecomment-909491913  

The effort stalled, but I did start investigating testing under QEMO for some of these platforms, it may be time to restart that.... the basic problem is that everything takes so looooong under emulation.

---

## Comment 5

> Username: tuliom  
> Created at: 2021-08-31 19:08:03 UTC  
> Url: https://github.com/boostorg/math/issues/682#issuecomment-909524337  

Would something like this be acceptable?  
```  
diff --git a/test/compile_test/cstdfloat_cmath_incl_test.cpp b/test/compile_test/cstdfloat_cmath_incl_test.cpp  
index 35894ec3f..d2b5df433 100644  
--- a/test/compile_test/cstdfloat_cmath_incl_test.cpp  
+++ b/test/compile_test/cstdfloat_cmath_incl_test.cpp  
@@ -53,7 +53,11 @@ void compile_and_link_test()  
     check_result<boost::float128_t>(std::fmax(f128, f128));  
     check_result<boost::float128_t>(std::fmin(f128, f128));  
     check_result<boost::float128_t>(std::fdim(f128, f128));  
+#if __LDBL_MANT_DIG__ == 113  
+    check_result<boost::float128_t>(std::nanl(0));  
+#else  
     check_result<boost::float128_t>(std::nanq(0));  
+#endif  
     check_result<boost::float128_t>(std::exp2(f128));  
     check_result<boost::float128_t>(std::log2(f128));  
     check_result<boost::float128_t>(std::log1p(f128));  
```

---

## Comment 6

> Username: jzmaddock  
> Created at: 2021-09-01 17:48:22 UTC  
> Url: https://github.com/boostorg/math/issues/682#issuecomment-910513145  

That's almost certainly the right fix.  
  
I've restarted trying to get the tests running on s390x / QEMO which also has a true 128-bit long double, most of the failures are related to the tests rather than the headers, but there are a few more substantive issues cropping up.  First run so we can see where we are is here: https://github.com/boostorg/math/pull/683/checks?check_run_id=3483182111
