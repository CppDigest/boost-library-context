# #460 - Configure error on ppc64le GNU/Linux [Open]

> Username: eschnett  
> Created at: 2021-01-08 19:16:16 UTC  
> Updated at: 2021-01-23 06:55:43 UTC  
> Url: https://github.com/boostorg/boost/issues/460  

I want to build `boost +mpi %gcc @10.2.0` via Spack https://github.com/spack/spack on Summit, which is a ppc64le GNU/Linux system. This fails due to a configure error in Boost.  
  
Although Spack calls Boost's `bootstrap.sh` as expected with `--with-toolset=gcc`, `bootstrap.sh` tries to use the `xlcc` toolset internally. The problem is that `bootstrap.sh` does not pass the `TOOLSET` variable as argument to the `build.sh` script.  
  
This patch solves the problem for me:  
  
```diff  
diff -u bootstrap.sh.orig bootstrap.sh  
--- a/bootstrap.sh.orig	2020-12-03 00:00:59.000000000 -0500  
+++ a/bootstrap.sh	2021-01-08 13:38:30.000000000 -0500  
@@ -223,7 +223,7 @@  
 if test "x$BJAM" = x; then  
   $ECHO "Building B2 engine.."  
   pwd=`pwd`  
-  (cd "$my_dir/tools/build/src/engine" && ./build.sh)  
+  (cd "$my_dir/tools/build/src/engine" && ./build.sh "$TOOLSET")  
   if [ $? -ne 0 ]; then  
       echo  
       echo "Failed to build B2 build engine"  
```

---

## Comment 1

> Username: grafikrobot  
> Created at: 2021-01-09 06:24:25 UTC  
> Url: https://github.com/boostorg/boost/issues/460#issuecomment-757104906  

The behavior of the B2 `build.sh` not taking the `$TOOLSET` is intentional. The b2 `build.sh` determines a toolset to build itself with automatically. If it's picking xlcc it means that's what it's finding as the first available and recognized compiler command. If that's not as intended it would point towards a misconfiguration in the system. I.e. how the spack setup it put together in your case. You have the option of altering the configuration such that `g++` is first in the PATH. It would help us give you better answers if you provide the following information: What Boost version are you building? How the `build.sh` fails? What is the environment when you run the build?

---

## Comment 2

> Username: eschnett  
> Created at: 2021-01-09 23:05:19 UTC  
> Url: https://github.com/boostorg/boost/issues/460#issuecomment-757380929  

I am building Boost 1.75.0.  
  
This is how Spack calls Boost:  
```  
==> boost: Executing phase: 'install'  
==> [2021-01-09-17:59:50.908972] './bootstrap.sh' '--prefix=/autofs/nccs-svm1_home1/schnett/src/CarpetX/spack/opt/spack/linux-rhel7-power9le/gcc-10.2.0/boost-1.75.0-fynzz7kaginesrsesqd44dlbgsil7ege' '--with-toolset=gcc' '--with-libraries=locale,random,wave,atomic,serialization,mpi,iostreams,regex,date_time,exception,log,thread,chrono,graph,system,timer,program_options,filesystem,test,math,graph_parallel'  
Building B2 engine..  
  
###  
###  
### Using 'xlcpp' toolset.  
###  
###  
  
> /autofs/nccs-svm1_home1/schnett/src/CarpetX/spack/lib/spack/env/gcc/g++ -qversion  
g++: error: unrecognized command-line option '-qversion'; did you mean '--version'?  
  
Failed to build B2 build engine  
```  
  
This is `PATH`:  
```  
PATH=/autofs/nccs-svm1_home1/schnett/src/CarpetX/spack/lib/spack/env/gcc:/autofs/nccs-svm1_home1/schnett/src/CarpetX/spack/lib/spack/env/case-insensitive:/autofs/nccs-svm1_home1/schnett/src/CarpetX/spack/lib/spack/env:/autofs/nccs-svm1_home1/schnett/src/CarpetX/spack/opt/spack/linux-rhel7-power9le/gcc-10.2.0/bzip2-1.0.8-4ensukxpdhjtidub7bnogtzmm5hysisc/bin:/autofs/nccs-svm1_home1/schnett/src/CarpetX/spack/opt/spack/linux-rhel7-power9le/gcc-10.2.0/openmpi-4.0.5-iroackgonywitgb2wa5iuceegmx2rqap/bin:/autofs/nccs-svm1_home1/schnett/src/CarpetX/spack/opt/spack/linux-rhel7-power9le/gcc-10.2.0/bzip2-1.0.8-4ensukxpdhjtidub7bnogtzmm5hysisc/bin:/autofs/nccs-svm1_home1/schnett/src/CarpetX/spack/opt/spack/linux-rhel7-power9le/gcc-10.2.0/openmpi-4.0.5-iroackgonywitgb2wa5iuceegmx2rqap/bin:/ccs/home/schnett/src/CarpetX/spack/bin:/sw/sources/lsf-tools/2.0/summit/bin:/sw/summit/xalt/1.2.1/bin:/autofs/nccs-svm1_sw/summit/.swci/1-compute/opt/spack/20180914/linux-rhel7-ppc64le/gcc-4.8.5/darshan-runtime-3.1.7-cnvxicgf5j4ap64qi6v5gxp67hmrjz43/bin:/sw/sources/hpss/bin:/autofs/nccs-svm1_sw/summit/.swci/1-compute/opt/spack/20180914/linux-rhel7-ppc64le/xl-16.1.1-5/spectrum-mpi-10.3.1.2-20200121-p6nrnt6vtvkn356wqg6f74n6jspnpjd2/bin:/sw/summit/xl/16.1.1-5/xlC/16.1.1/bin:/sw/summit/xl/16.1.1-5/xlf/16.1.1/bin:/opt/ibm/spectrumcomputing/lsf/10.1.0.9/linux3.10-glibc2.17-ppc64le-csm/etc:/opt/ibm/spectrumcomputing/lsf/10.1.0.9/linux3.10-glibc2.17-ppc64le-csm/bin:/opt/ibm/csm/bin:/ccs/home/schnett/bin:/ccs/home/schnett/src/spack-tmux/bin:/autofs/nccs-svm1_sw/summit/.swci/0-core/opt/spack/20180914/linux-rhel7-ppc64le/gcc-4.8.5/tmux-2.2-z2cgytxdo3rzw643uj2fiwp7iwqbbbwp/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/opt/ibm/flightlog/bin:/opt/ibutils/bin:/opt/ibm/spectrum_mpi/jsm_pmix/bin:/opt/puppetlabs/bin:/usr/lpp/mmfs/bin  
```  
  
Spack adds GCC to the front of the path, offering both the names `c++` and `g++`. Spack also sets the environment variable `CXX` to the path of this `c++`:  
```  
CXX=/autofs/nccs-svm1_home1/schnett/src/CarpetX/spack/lib/spack/env/gcc/g++  
```  
  
I assume the following happens:  
  
- The script `build.sh` is indeed using the GNU compiler. I assume this is so because the environment variable `CXX` is set.  
- The script also decides that it wants to use the `xlcpp` toolset. I assume this is because the function `guess_toolset` doesn't look at `CXX`; instead, the condition is `test_uname Linux && test_path xlC_r`, i.e. it checks whether `xlC_r` is present. It is, but later in `PATH` than GCC.  
- Later, the script then passes the argument `-qversion` to the GNU compiler, which fails, aborting the build.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2021-01-10 18:09:11 UTC  
> Url: https://github.com/boostorg/boost/issues/460#issuecomment-757518460  

Indeed that's likely what happens. You can likely resolve it by clearing `CXX` and `CXFLAGS` before running `bootstrap.sh`.

---

## Comment 4

> Username: Rashika101  
> Created at: 2021-01-23 06:55:43 UTC  
> Url: https://github.com/boostorg/boost/issues/460#issuecomment-765879607  

Hey!!  
are you willing to accept new contributors? I am interested into contributing to this repository so, if you could explain a bit more this feature request, it would be great.  
Thanks
