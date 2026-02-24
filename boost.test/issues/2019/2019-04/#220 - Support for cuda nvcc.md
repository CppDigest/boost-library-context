# #220 - Support for cuda nvcc [Closed]

> Username: Char-Aznable  
> Created at: 2019-04-30 17:44:51 UTC  
> Updated at: 2020-06-05 10:08:26 UTC  
> Closed at: 2020-03-29 16:28:42 UTC  
> Url: https://github.com/boostorg/test/issues/220  

I have been using Boost.Test to test some my GPU  code. The usage is do some computation on the GPU, sync the result back to host and compare it with the result computed on the host. Until boost 1.68 this works perfectly. But in boost 1.69, I'm no longer able to compile Boost.Test with the CUDA compiler nvcc. Consider this:  
```  
#define BOOST_TEST_MODULE example  
#include <boost/test/included/unit_test.hpp>  
  
BOOST_AUTO_TEST_CASE( free_test_function )  
{  
BOOST_TEST( true );  
}  
  
```  
Compiling this with "nvcc -x cu test.cpp -o test -std=c++14 -I$BOOST_ROOT/include" gives error:  
```  
test.cpp(7): error: identifier "BOOST_TEST_TOOL_UNIV" is undefined  
  
test.cpp(7): error: identifier "BOOST_TEST_TOOL_UNIV_EX" is undefined  
  
test.cpp(7): error: identifier "CHECK" is undefined  
  
test.cpp(7): error: identifier "BOOST_TEST_INVOKE_IF_N_ARGS" is undefined  
  
4 errors detected in the compilation of "/tmp/64641903.1.login.q/tmpxft_0002a275_00000000-8_test.cpp1.ii"  
```  
where "$BOOST_ROOT" is where boost is installed. This example compiles and runs fine with gcc and clang.   
  
If I change line 83 of `$BOOST_ROOT/include/boost/preprocessor/config/config.hpp` from   
```  
if defined __GCCXML__ || defined __CUDACC__ || defined __PATHSCALE__ || defined __DMC__ || defined __CODEGEARC__ || defined __BORLANDC__ || defined __MWERKS__ || ( defined __SUNPRO_CC && __SUNPRO_CC < 0x5120 ) || defined __HP_aCC && !defined __EDG__ || defined __MRC__ || defined __SC__ || (defined(__PGI) && !defined(__EDG__))  
```  
to   
```  
if defined __GCCXML__ || defined __PATHSCALE__ || defined __DMC__ || defined __CODEGEARC__ || defined __BORLANDC__ || defined __MWERKS__ || ( defined __SUNPRO_CC && __SUNPRO_CC < 0x5120 ) || defined __HP_aCC && !defined __EDG__ || defined __MRC__ || defined __SC__ || (defined(__PGI) && !defined(__EDG__))  
```  
then the program works fine. Can I know the reason why nvcc is/was not supported?

---

## Comment 1

> Username: masterleinad  
> Created at: 2019-05-23 16:07:50 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-495283248  

I see the same issue with `pgc++ 18.10-1` in `boost-1.67.0`.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-10-27 13:42:11 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-546695836  

I believe for the CUDA case this is solved by https://github.com/boostorg/preprocessor/commit/c436de6142477dd2c432e0836004c2503ef3b0f1 and that should be in for Boost 1.72.  
  
Would it be possible to give a try @Char-Aznable ?  
  
@masterleinad I do not know about `pgc++` support in Boost. I can raise create a ticket on boost.preprocessor. In the meantime, would you please dump all the defines of your compiler? (like https://stackoverflow.com/a/2224357/1617295)

---

## Comment 3

> Username: aprokop  
> Created at: 2019-10-28 17:54:06 UTC  
> Updated at: 2019-10-28 17:55:11 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-547069847  

@raffienficiaud in arborx/ArborX#168 we also encountered this issue. I tried all boost 1.68-1.71, and only 1.68 works. The patch that you linked to did not fix the issue. In my case, I'm using gcc 7.4 with cuda 10.1.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2019-10-28 22:23:22 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-547171211  

Thanks for the update, I will have a look

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2019-12-12 23:52:43 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-565239470  

Hi,  
  
I created a branch `topic/GH-220-support-nvcc` with a blind fix on this topic. Would it be possible for anyone to test that branch?  
  
Thanks!

---

## Comment 6

> Username: aprokop  
> Created at: 2019-12-18 21:41:43 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-567225523  

@raffienficiaud It's a bit hard for me to test this in its current form (I use spack to build boost packages). It is possible to instead install something like 1.71.0, and then patch the installation instead?

---

## Comment 7

> Username: Char-Aznable  
> Created at: 2019-12-18 22:29:11 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-567241533  

@raffienficiaud I manually applied the changes in branch `topic/GH-220-support-nvcc` to my pre-installed 1.71.0 and they work for me.

---

## Comment 8

> Username: raffienficiaud  
> Created at: 2019-12-18 22:30:07 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-567241774  

@Char-Aznable thanks for the feedback, @aprokop maybe this approach works for you as well?

---

## Comment 9

> Username: aprokop  
> Created at: 2019-12-18 22:52:34 UTC  
> Updated at: 2019-12-18 22:57:33 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-567248436  

I patched 1.71.0 install. Nothing changed, and I still observe the same errors. The patch I applied was taken to be a single commit from that branch:  
```diff  
diff --git a/include/boost/test/detail/config.hpp b/include/boost/test/detail/config.hpp                                                                                                                           
index 5130def674..df69b2d158 100644                                                                                                                                                                                
--- a/include/boost/test/detail/config.hpp                                                                                                                                                                         
+++ b/include/boost/test/detail/config.hpp                                                                                                                                                                         
@@ -139,6 +139,10 @@ class type_info;                                                                                                                                                                              
 #define BOOST_PP_VARIADICS 1                                                                                                                                                                                      
 #endif                                                                                                                                                                                                            
                                                                                                                                                                                                                   
+#if defined(__NVCC__)                                                                                                                                                                                             
+#define BOOST_PP_VARIADICS 1                                                                                                                                                                                      
+#endif                                                                                                                                                                                                            
+                                                                                                                                                                                                                  
 #endif /* ifndef BOOST_PP_VARIADICS */                                                                                                                                                                            
                                                                                                                                                                                                                   
 //____________________________________________________________________________//                                                                                                                                  
diff --git a/include/boost/test/unit_test_suite.hpp b/include/boost/test/unit_test_suite.hpp                                                                                                                       
index be862ef217..f74a4c47a5 100644                                                                                                                                                                                
--- a/include/boost/test/unit_test_suite.hpp                                                                                                                                                                       
+++ b/include/boost/test/unit_test_suite.hpp                                                                                                                                                                       
@@ -13,6 +13,7 @@                                                                                                                                                                                                  
 #define BOOST_TEST_UNIT_TEST_SUITE_HPP_071894GER                                                                                                                                                                  
                                                                                                                                                                                                                   
 // Boost.Test                                                                                                                                                                                                     
+#include <boost/test/detail/config.hpp>                                                                                                                                                                           
 #include <boost/test/framework.hpp>                                                                                                                                                                               
 #include <boost/test/tree/auto_registration.hpp>                                                                                                                                                                  
 #include <boost/test/tree/test_case_template.hpp>     
```  
and I checked the resulting files that it indeed applied.  
  
I also checked that we indeed enter `#if defined(__NVCC__)` clause through putting `#error` there.

---

## Comment 10

> Username: raffienficiaud  
> Created at: 2019-12-18 23:08:16 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-567253099  

> I also checked that we indeed enter #if defined(__NVCC__) clause through putting #error there.  
  
so we *do* enter this part, correct? OTOH you are saying that you use GCC 7.4, I am a bit confused :)

---

## Comment 11

> Username: aprokop  
> Created at: 2019-12-18 23:12:45 UTC  
> Updated at: 2019-12-18 23:13:04 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-567254379  

> so we _do_ enter this part, correct?  
  
yes  
  
> OTOH you are saying that you use GCC 7.4, I am a bit confused :)  
  
7.4 is host compiler. Our package uses Kokkos, and its `nvcc_wrapper` tool which chooses the compiler depending on whether the file is being compiled for host or device. This should not matter, the errors are definitely coming from nvcc, based on their format (parenthesis, like `../test/tstHeapOperations.cpp(183): error`).

---

## Comment 12

> Username: Char-Aznable  
> Created at: 2019-12-19 00:04:45 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-567268414  

@aprokop Are you using header-only Boost.Test, the dynamic or static lib linked one?

---

## Comment 13

> Username: aprokop  
> Created at: 2019-12-19 21:07:16 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-567677189  

@Char-Aznable We do dynamic linking  
```  
target_compile_definitions(ArborX_LinearBVH.exe PRIVATE BOOST_TEST_DYN_LINK)  
```

---

## Comment 14

> Username: raffienficiaud  
> Created at: 2019-12-20 06:21:19 UTC  
> Updated at: 2019-12-20 06:21:59 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-567805223  

@aprokop  It would be helpful if you can provide me with the command line that is emitted by the compiler. Since you are using CMake, you can maybe use the make generator and use `VERBOSE=1`, and provide me with the command line the build system emits on the file that has the compilation issue.  
  
I also assume that you have the exact same error as the one reported in the initial issue definition. If this is not the case, please provide me with the exact error as well.  
  
Thanks!

---

## Comment 15

> Username: aprokop  
> Created at: 2019-12-20 13:06:50 UTC  
> Updated at: 2019-12-20 13:07:59 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-567918317  

```  
ccache /home/xap/local/opt/kokkos/bin/nvcc_wrapper  -DBOOST_ALL_NO_LIB -DBOOST_TEST_DYN_LINK -DBOOST_UNIT_TEST_FRAMEWORK_DYN_LINK -I../src -I../src/details -Iinclude -isystem /home/xap/local/opt/kokkos/include  
 -isystem /home/xap/local/opt/spack/opt/spack/linux-ubuntu18.04-broadwell/gcc-7.4.0/openmpi-3.1.4-oetr45yq7vnbcqhiiaalfkpoftuujlzn/include -isystem /home/xap/local/opt/spack/opt/spack/linux-ubuntu18.04-broadwe  
ll/gcc-7.4.0/boost-1.71.0-tfmutpkhlyvlinpsbedzdrtmp7aapbrd/include -lineinfo -DKOKKOS_ENABLE_PROFILING -O2 -g -DNDEBUG   --std=c++14 -Xcudafe --diag_suppress=esa_on_defaulted_function_ignored -expt-extended-la  
mbda -arch=sm_61 -Xcompiler -fopenmp -fdiagnostics-color -MD -MT test/CMakeFiles/ArborX_DetailsTreeTraversal.exe.dir/tstDetailsTreeTraversal.cpp.o -MF test/CMakeFiles/ArborX_DetailsTreeTraversal.exe.dir/tstDet  
ailsTreeTraversal.cpp.o.d -o test/CMakeFiles/ArborX_DetailsTreeTraversal.exe.dir/tstDetailsTreeTraversal.cpp.o -c ../test/tstDetailsTreeTraversal.cpp  
../test/tstDetailsTreeTraversal.cpp(36): error: identifier "BOOST_TEST_TOOL_UNIV" is undefined  
  
../test/tstDetailsTreeTraversal.cpp(36): error: identifier "BOOST_TEST_TOOL_UNIV_EX" is undefined  
  
../test/tstDetailsTreeTraversal.cpp(36): error: identifier "CHECK" is undefined  
```  
I have the following modules loaded:  
```  
 1) cuda/10.1   2) benchmark-1.5.0-gcc-7.4.0-ewymu2d   3) cmake-3.15.4-gcc-7.4.0-gqckk2m   4) openmpi-3.1.4-gcc-7.4.0-oetr45y   5) boost-1.71.0-gcc-7.4.0-tfmutpk    
```  
The boost 1.71.0 module was patched with the proposed fix.

---

## Comment 16

> Username: Char-Aznable  
> Created at: 2020-01-02 23:58:11 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-570406421  

@aprokop The OP is a header-only use case so yours could be a different issue. I also tried using kokkos' nvcc_wrapper with the proposed patch and it worked fine. Along the same lines,  I am not sure how the patch here would interact with the installed dynamic libs so header patch might not work for you. I also assume you have clean the ccache for other potential confounding factors? Do you have a minimal reproducing case?

---

## Comment 17

> Username: Char-Aznable  
> Created at: 2020-01-16 23:03:12 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-575387987  

@raffienficiaud For the sake of time, can we conclude your proposed patch fix the issue? Maybe @aprokop can open another issue with example code for your case? I desperately need this because I can't afford patching up all the machines manually.

---

## Comment 18

> Username: aprokop  
> Created at: 2020-01-16 23:04:48 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-575388502  

I have no objections. I don't have a reproducer right now, and this is not a priority for me.

---

## Comment 19

> Username: raffienficiaud  
> Created at: 2020-03-29 16:28:42 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-605662706  

Fixed in master

---

## Comment 20

> Username: correaa  
> Created at: 2020-06-05 08:32:12 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-639338869  

Not sure if this is the conclusion of this thread but adding   
```c++  
#if defined(__NVCC__)  
#define BOOST_PP_VARIADICS 1  
#endif  
```  
before incluing `boost/test/unit_test.hpp` seems to solve the problem, in Boost. 1.71 at least.

---

## Comment 21

> Username: raffienficiaud  
> Created at: 2020-06-05 10:08:25 UTC  
> Url: https://github.com/boostorg/test/issues/220#issuecomment-639384797  

This is fixed in 1.73, would you please be able to check if that fix works for you @correaa ? If not please open a new ticket.
