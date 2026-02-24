# #259 - Help needed with trying out Boost.Histogram [Closed]

> Username: jvo203  
> Created at: 2019-12-12 01:33:51 UTC  
> Updated at: 2019-12-16 01:56:43 UTC  
> Closed at: 2019-12-13 13:37:44 UTC  
> Url: https://github.com/boostorg/histogram/issues/259  

Including #include <boost/histogram.hpp> results in a compilation error:  
  
histogram/include/boost/histogram/accumulators/mean.hpp(11): 致命的なエラー: ソース ファイル "boost/core/nvp.hpp" を開けません。  
  #include <boost/core/nvp.hpp>  
  
Searching for nvp.hpp reveals its true location to be different:  
  
/usr/include/boost/serialization/nvp.hpp  
  
I am on Boost 1.71 in openSUSE Tumbleweed:  
  
#define BOOST_VERSION 107100

---

## Comment 1

> Username: henryiii  
> Created at: 2019-12-12 03:06:31 UTC  
> Updated at: 2019-12-12 03:06:40 UTC  
> Url: https://github.com/boostorg/histogram/issues/259#issuecomment-564829624  

Actually the problem is that your Boost version is too old. You should either use 1.72 and the current version of Boost Histogram, or 1.71 and the 1.71 version of Boost-Histogram (which is already there in `/usr/include/boost/histogram`).  
  
If you do want to use the latest version along with an older version of Boost installed in your system includes, you'll have to make sure that the local includes (1.72 version) supersede the system ones. Are you building the tests with the built-in CMake downloading Boost, or something else?

---

## Comment 2

> Username: jvo203  
> Created at: 2019-12-12 04:32:04 UTC  
> Updated at: 2019-12-16 01:56:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/259#issuecomment-564845093  

Hi Henry,  
  
Thanks for a prompt reply. I see :-)   
  
I am not building any tests, just trying to use boost/histogram in an astronomical project instead of the currently used CERN ROOT.  
  
Indeed there is /usr/include/boost/histogram in my system! So in this case I will switch from the github version to whatever comes installed by default in the rolling-release openSUSE Tumbleweed.

---

## Comment 3

> Username: jvo203  
> Created at: 2019-12-12 04:38:53 UTC  
> Url: https://github.com/boostorg/histogram/issues/259#issuecomment-564846239  

Unfortunately there is a mass of errors after using the system boost/histogram. My project uses C++17, not C++14, perhaps this has something to do with it. Simply including #include <boost/histogram.hpp> produces a long list of errors seen in the attached file:  
  
[log.txt](https://github.com/boostorg/histogram/files/3953840/log.txt)

---

## Comment 4

> Username: henryiii  
> Created at: 2019-12-12 14:20:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/259#issuecomment-565025919  

Are you sure that you are using C++17 and not 11? `std::common_type_t` was introduced in C++14. I've checked the file in 1.71, and it includes `<type_traits>`, so your first error should not happen (and is what you generally see if you are trying to compile in C++11 mode). Can you add "verbose" building so you can see what arguments are going to the compiler? I would have thought modern GCC would default to a proper value, so maybe another library is injecting a c++11 flag.

---

## Comment 5

> Username: jvo203  
> Created at: 2019-12-13 00:52:56 UTC  
> Url: https://github.com/boostorg/histogram/issues/259#issuecomment-565253812  

You were right. Indeed something else is injecting C++11 right after my C++17 flag! The code compiled OK this time because the boost/histogram header had been commented out.  
  
"-g -O3 -Wno-register -std=c++17 -std=c++11 -version -fopenmp -fopenmp-simd -funroll-loops -ftree-vectorize"  
  
The compiler identifies itself by "GNU C++11 (SUSE Linux) version 9.2.1 20190903 [gcc-9-branch revision 275330] (x86_64-suse-linux)" so perhaps it keeps injecting C++11 irrespective of any user flags?  
  
Would you happen to have any ideas how to find out what might be doing it and how to disable any C++11? I have never heard of a library forcefully injecting a C++ standard, hence overriding any gcc user flags. It is very perplexing...  
  
g++ -v -march=native -g -O3 -std=c++17 -fopenmp -fopenmp-simd -funroll-loops -ftree-vectorize -Wno-register -DLIBUS_NO_SSL -DHAVE_INLINE -I/usr/include/postgresql `/home/chris/root/bin/root-config --cflags` src/json.c src/main.cpp src/SeedHist2D.cpp src/PJMCoords.cc -o gaiawebql -lstdc++fs -lpq -luWS -lcurl -lcrypto -lssl -lz -l:libnuma.so.1 -lpthread `/home/chris/root/bin/root-config --libs` -L`jemalloc-config --libdir` -Wl,-rpath,`jemalloc-config --libdir` -l:libjemalloc.so.2 `jemalloc-config --libs`  
Using built-in specs.  
COLLECT_GCC=g++  
COLLECT_LTO_WRAPPER=/usr/lib64/gcc/x86_64-suse-linux/9/lto-wrapper  
OFFLOAD_TARGET_NAMES=hsa:nvptx-none  
Target: x86_64-suse-linux  
Configured with: ../configure --prefix=/usr --infodir=/usr/share/info --mandir=/usr/share/man --libdir=/usr/lib64 --libexecdir=/usr/lib64 --enable-languages=c,c++,objc,fortran,obj-c++,ada,go,d --enable-offload-targets=hsa,nvptx-none=/usr/nvptx-none, --without-cuda-driver --disable-werror --with-gxx-include-dir=/usr/include/c++/9 --enable-ssp --disable-libssp --disable-libvtv --disable-cet --disable-libcc1 --enable-plugin --with-bugurl=https://bugs.opensuse.org/ --with-pkgversion='SUSE Linux' --with-slibdir=/lib64 --with-system-zlib --enable-libstdcxx-allocator=new --disable-libstdcxx-pch --enable-libphobos --enable-version-specific-runtime-libs --with-gcc-major-version-only --enable-linker-build-id --enable-linux-futex --enable-gnu-indirect-function --program-suffix=-9 --without-system-libunwind --enable-multilib --with-arch-32=x86-64 --with-tune=generic --with-build-config=bootstrap-lto-lean --enable-link-mutex --build=x86_64-suse-linux --host=x86_64-suse-linux  
Thread model: posix  
gcc version 9.2.1 20190903 [gcc-9-branch revision 275330] (SUSE Linux)   
COLLECT_GCC_OPTIONS='-v' '-march=native' '-g' '-O3' '-std=c++17' '-fopenmp' '-fopenmp-simd' '-funroll-loops' '-ftree-vectorize' '-Wno-register' '-D' 'LIBUS_NO_SSL' '-D' 'HAVE_INLINE' '-I' '/usr/include/postgresql' '-pthread' '-std=c++11' '-m64' '-I' '/home/chris/root/include' '-o' 'gaiawebql' '-L/home/chris/root/lib' '-pthread' '-rdynamic' '-L/usr/lib64' '-pthread' '-shared-libgcc' '-pthread'  
 /usr/lib64/gcc/x86_64-suse-linux/9/cc1plus -quiet -v -I /usr/include/postgresql -I /home/chris/root/include -D_GNU_SOURCE -D_REENTRANT -D LIBUS_NO_SSL -D HAVE_INLINE src/json.c -march=broadwell -mmmx -mno-3dnow -msse -msse2 -msse3 -mssse3 -mno-sse4a -mcx16 -msahf -mmovbe -maes -mno-sha -mpclmul -mpopcnt -mabm -mno-lwp -mfma -mno-fma4 -mno-xop -mbmi -mno-sgx -mbmi2 -mno-pconfig -mno-wbnoinvd -mno-tbm -mavx -mavx2 -msse4.2 -msse4.1 -mlzcnt -mrtm -mhle -mrdrnd -mf16c -mfsgsbase -mrdseed -mprfchw -madx -mfxsr -mxsave -mxsaveopt -mno-avx512f -mno-avx512er -mno-avx512cd -mno-avx512pf -mno-prefetchwt1 -mno-clflushopt -mno-xsavec -mno-xsaves -mno-avx512dq -mno-avx512bw -mno-avx512vl -mno-avx512ifma -mno-avx512vbmi -mno-avx5124fmaps -mno-avx5124vnniw -mno-clwb -mno-mwaitx -mno-clzero -mno-pku -mno-rdpid -mno-gfni -mno-shstk -mno-avx512vbmi2 -mno-avx512vnni -mno-vaes -mno-vpclmulqdq -mno-avx512bitalg -mno-movdiri -mno-movdir64b -mno-waitpkg -mno-cldemote -mno-ptwrite --param l1-cache-size=32 --param l1-cache-line-size=64 --param l2-cache-size=15360 -mtune=broadwell -quiet -dumpbase json.c -m64 -auxbase json -g -O3 -Wno-register -std=c++17 -std=c++11 -version -fopenmp -fopenmp-simd -funroll-loops -ftree-vectorize -o /tmp/ccem2p3h.s  
GNU C++11 (SUSE Linux) version 9.2.1 20190903 [gcc-9-branch revision 275330] (x86_64-suse-linux)  
        compiled by GNU C version 9.2.1 20190903 [gcc-9-branch revision 275330], GMP version 6.1.2, MPFR version 4.0.2, MPC version 1.1.0, isl version isl-0.20-GMP  
  
GGC heuristics: --param ggc-min-expand=100 --param ggc-min-heapsize=131072  
ignoring nonexistent directory "/usr/include/postgresql"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/ipp/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/mkl/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/pstl/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/daal/include"  
#include "..." search starts here:  
#include <...> search starts here:  
 /home/chris/root/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/ipp/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/mkl/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/pstl/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/daal/include  
 /usr/include/c++/9  
 /usr/include/c++/9/x86_64-suse-linux  
 /usr/include/c++/9/backward  
 /usr/lib64/gcc/x86_64-suse-linux/9/include  
 /usr/local/include  
 /usr/lib64/gcc/x86_64-suse-linux/9/include-fixed  
 /usr/lib64/gcc/x86_64-suse-linux/9/../../../../x86_64-suse-linux/include  
 /usr/include  
End of search list.  
GNU C++11 (SUSE Linux) version 9.2.1 20190903 [gcc-9-branch revision 275330] (x86_64-suse-linux)  
        compiled by GNU C version 9.2.1 20190903 [gcc-9-branch revision 275330], GMP version 6.1.2, MPFR version 4.0.2, MPC version 1.1.0, isl version isl-0.20-GMP  
  
GGC heuristics: --param ggc-min-expand=100 --param ggc-min-heapsize=131072  
Compiler executable checksum: 00000000000000000000000000000000  
COLLECT_GCC_OPTIONS='-v' '-march=native' '-g' '-O3' '-std=c++17' '-fopenmp' '-fopenmp-simd' '-funroll-loops' '-ftree-vectorize' '-Wno-register' '-D' 'LIBUS_NO_SSL' '-D' 'HAVE_INLINE' '-I' '/usr/include/postgresql' '-pthread' '-std=c++11' '-m64' '-I' '/home/chris/root/include' '-o' 'gaiawebql' '-L/home/chris/root/lib' '-pthread' '-rdynamic' '-L/usr/lib64' '-pthread' '-shared-libgcc' '-pthread'  
 /usr/lib64/gcc/x86_64-suse-linux/9/../../../../x86_64-suse-linux/bin/as -v -I /usr/include/postgresql -I /home/chris/root/include --64 -o /tmp/cc08wUef.o /tmp/ccem2p3h.s  
GNU アセンブラ バージョン 2.33.1 (x86_64-suse-linux)、BFD バージョン (GNU Binutils; openSUSE Tumbleweed) 2.33.1.20191023-2 を使用  
COLLECT_GCC_OPTIONS='-v' '-march=native' '-g' '-O3' '-std=c++17' '-fopenmp' '-fopenmp-simd' '-funroll-loops' '-ftree-vectorize' '-Wno-register' '-D' 'LIBUS_NO_SSL' '-D' 'HAVE_INLINE' '-I' '/usr/include/postgresql' '-pthread' '-std=c++11' '-m64' '-I' '/home/chris/root/include' '-o' 'gaiawebql' '-L/home/chris/root/lib' '-pthread' '-rdynamic' '-L/usr/lib64' '-pthread' '-shared-libgcc' '-pthread'  
 /usr/lib64/gcc/x86_64-suse-linux/9/cc1plus -quiet -v -I /usr/include/postgresql -I /home/chris/root/include -D_GNU_SOURCE -D_REENTRANT -D LIBUS_NO_SSL -D HAVE_INLINE src/main.cpp -march=broadwell -mmmx -mno-3dnow -msse -msse2 -msse3 -mssse3 -mno-sse4a -mcx16 -msahf -mmovbe -maes -mno-sha -mpclmul -mpopcnt -mabm -mno-lwp -mfma -mno-fma4 -mno-xop -mbmi -mno-sgx -mbmi2 -mno-pconfig -mno-wbnoinvd -mno-tbm -mavx -mavx2 -msse4.2 -msse4.1 -mlzcnt -mrtm -mhle -mrdrnd -mf16c -mfsgsbase -mrdseed -mprfchw -madx -mfxsr -mxsave -mxsaveopt -mno-avx512f -mno-avx512er -mno-avx512cd -mno-avx512pf -mno-prefetchwt1 -mno-clflushopt -mno-xsavec -mno-xsaves -mno-avx512dq -mno-avx512bw -mno-avx512vl -mno-avx512ifma -mno-avx512vbmi -mno-avx5124fmaps -mno-avx5124vnniw -mno-clwb -mno-mwaitx -mno-clzero -mno-pku -mno-rdpid -mno-gfni -mno-shstk -mno-avx512vbmi2 -mno-avx512vnni -mno-vaes -mno-vpclmulqdq -mno-avx512bitalg -mno-movdiri -mno-movdir64b -mno-waitpkg -mno-cldemote -mno-ptwrite --param l1-cache-size=32 --param l1-cache-line-size=64 --param l2-cache-size=15360 -mtune=broadwell -quiet -dumpbase main.cpp -m64 -auxbase main -g -O3 -Wno-register -std=c++17 -std=c++11 -version -fopenmp -fopenmp-simd -funroll-loops -ftree-vectorize -o /tmp/ccem2p3h.s  
GNU C++11 (SUSE Linux) version 9.2.1 20190903 [gcc-9-branch revision 275330] (x86_64-suse-linux)  
        compiled by GNU C version 9.2.1 20190903 [gcc-9-branch revision 275330], GMP version 6.1.2, MPFR version 4.0.2, MPC version 1.1.0, isl version isl-0.20-GMP  
  
GGC heuristics: --param ggc-min-expand=100 --param ggc-min-heapsize=131072  
ignoring nonexistent directory "/usr/include/postgresql"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/ipp/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/mkl/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/pstl/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/daal/include"  
#include "..." search starts here:  
#include <...> search starts here:  
 /home/chris/root/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/ipp/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/mkl/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/pstl/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/daal/include  
 /usr/include/c++/9  
 /usr/include/c++/9/x86_64-suse-linux  
 /usr/include/c++/9/backward  
 /usr/lib64/gcc/x86_64-suse-linux/9/include  
 /usr/local/include  
 /usr/lib64/gcc/x86_64-suse-linux/9/include-fixed  
 /usr/lib64/gcc/x86_64-suse-linux/9/../../../../x86_64-suse-linux/include  
 /usr/include  
End of search list.  
GNU C++11 (SUSE Linux) version 9.2.1 20190903 [gcc-9-branch revision 275330] (x86_64-suse-linux)  
        compiled by GNU C version 9.2.1 20190903 [gcc-9-branch revision 275330], GMP version 6.1.2, MPFR version 4.0.2, MPC version 1.1.0, isl version isl-0.20-GMP  
  
GGC heuristics: --param ggc-min-expand=100 --param ggc-min-heapsize=131072  
Compiler executable checksum: 00000000000000000000000000000000  
COLLECT_GCC_OPTIONS='-v' '-march=native' '-g' '-O3' '-std=c++17' '-fopenmp' '-fopenmp-simd' '-funroll-loops' '-ftree-vectorize' '-Wno-register' '-D' 'LIBUS_NO_SSL' '-D' 'HAVE_INLINE' '-I' '/usr/include/postgresql' '-pthread' '-std=c++11' '-m64' '-I' '/home/chris/root/include' '-o' 'gaiawebql' '-L/home/chris/root/lib' '-pthread' '-rdynamic' '-L/usr/lib64' '-pthread' '-shared-libgcc' '-pthread'  
 /usr/lib64/gcc/x86_64-suse-linux/9/../../../../x86_64-suse-linux/bin/as -v -I /usr/include/postgresql -I /home/chris/root/include --64 -o /tmp/ccuAj3lf.o /tmp/ccem2p3h.s  
GNU アセンブラ バージョン 2.33.1 (x86_64-suse-linux)、BFD バージョン (GNU Binutils; openSUSE Tumbleweed) 2.33.1.20191023-2 を使用  
COLLECT_GCC_OPTIONS='-v' '-march=native' '-g' '-O3' '-std=c++17' '-fopenmp' '-fopenmp-simd' '-funroll-loops' '-ftree-vectorize' '-Wno-register' '-D' 'LIBUS_NO_SSL' '-D' 'HAVE_INLINE' '-I' '/usr/include/postgresql' '-pthread' '-std=c++11' '-m64' '-I' '/home/chris/root/include' '-o' 'gaiawebql' '-L/home/chris/root/lib' '-pthread' '-rdynamic' '-L/usr/lib64' '-pthread' '-shared-libgcc' '-pthread'  
 /usr/lib64/gcc/x86_64-suse-linux/9/cc1plus -quiet -v -I /usr/include/postgresql -I /home/chris/root/include -D_GNU_SOURCE -D_REENTRANT -D LIBUS_NO_SSL -D HAVE_INLINE src/SeedHist2D.cpp -march=broadwell -mmmx -mno-3dnow -msse -msse2 -msse3 -mssse3 -mno-sse4a -mcx16 -msahf -mmovbe -maes -mno-sha -mpclmul -mpopcnt -mabm -mno-lwp -mfma -mno-fma4 -mno-xop -mbmi -mno-sgx -mbmi2 -mno-pconfig -mno-wbnoinvd -mno-tbm -mavx -mavx2 -msse4.2 -msse4.1 -mlzcnt -mrtm -mhle -mrdrnd -mf16c -mfsgsbase -mrdseed -mprfchw -madx -mfxsr -mxsave -mxsaveopt -mno-avx512f -mno-avx512er -mno-avx512cd -mno-avx512pf -mno-prefetchwt1 -mno-clflushopt -mno-xsavec -mno-xsaves -mno-avx512dq -mno-avx512bw -mno-avx512vl -mno-avx512ifma -mno-avx512vbmi -mno-avx5124fmaps -mno-avx5124vnniw -mno-clwb -mno-mwaitx -mno-clzero -mno-pku -mno-rdpid -mno-gfni -mno-shstk -mno-avx512vbmi2 -mno-avx512vnni -mno-vaes -mno-vpclmulqdq -mno-avx512bitalg -mno-movdiri -mno-movdir64b -mno-waitpkg -mno-cldemote -mno-ptwrite --param l1-cache-size=32 --param l1-cache-line-size=64 --param l2-cache-size=15360 -mtune=broadwell -quiet -dumpbase SeedHist2D.cpp -m64 -auxbase SeedHist2D -g -O3 -Wno-register -std=c++17 -std=c++11 -version -fopenmp -fopenmp-simd -funroll-loops -ftree-vectorize -o /tmp/ccem2p3h.s  
GNU C++11 (SUSE Linux) version 9.2.1 20190903 [gcc-9-branch revision 275330] (x86_64-suse-linux)  
        compiled by GNU C version 9.2.1 20190903 [gcc-9-branch revision 275330], GMP version 6.1.2, MPFR version 4.0.2, MPC version 1.1.0, isl version isl-0.20-GMP  
  
GGC heuristics: --param ggc-min-expand=100 --param ggc-min-heapsize=131072  
ignoring nonexistent directory "/usr/include/postgresql"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/ipp/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/mkl/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/pstl/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/daal/include"  
#include "..." search starts here:  
#include <...> search starts here:  
 /home/chris/root/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/ipp/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/mkl/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/pstl/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/daal/include  
 /usr/include/c++/9  
 /usr/include/c++/9/x86_64-suse-linux  
 /usr/include/c++/9/backward  
 /usr/lib64/gcc/x86_64-suse-linux/9/include  
 /usr/local/include  
 /usr/lib64/gcc/x86_64-suse-linux/9/include-fixed  
 /usr/lib64/gcc/x86_64-suse-linux/9/../../../../x86_64-suse-linux/include  
 /usr/include  
End of search list.  
GNU C++11 (SUSE Linux) version 9.2.1 20190903 [gcc-9-branch revision 275330] (x86_64-suse-linux)  
        compiled by GNU C version 9.2.1 20190903 [gcc-9-branch revision 275330], GMP version 6.1.2, MPFR version 4.0.2, MPC version 1.1.0, isl version isl-0.20-GMP  
  
GGC heuristics: --param ggc-min-expand=100 --param ggc-min-heapsize=131072  
Compiler executable checksum: 00000000000000000000000000000000  
COLLECT_GCC_OPTIONS='-v' '-march=native' '-g' '-O3' '-std=c++17' '-fopenmp' '-fopenmp-simd' '-funroll-loops' '-ftree-vectorize' '-Wno-register' '-D' 'LIBUS_NO_SSL' '-D' 'HAVE_INLINE' '-I' '/usr/include/postgresql' '-pthread' '-std=c++11' '-m64' '-I' '/home/chris/root/include' '-o' 'gaiawebql' '-L/home/chris/root/lib' '-pthread' '-rdynamic' '-L/usr/lib64' '-pthread' '-shared-libgcc' '-pthread'  
 /usr/lib64/gcc/x86_64-suse-linux/9/../../../../x86_64-suse-linux/bin/as -v -I /usr/include/postgresql -I /home/chris/root/include --64 -o /tmp/ccEVG6gg.o /tmp/ccem2p3h.s  
GNU アセンブラ バージョン 2.33.1 (x86_64-suse-linux)、BFD バージョン (GNU Binutils; openSUSE Tumbleweed) 2.33.1.20191023-2 を使用  
COLLECT_GCC_OPTIONS='-v' '-march=native' '-g' '-O3' '-std=c++17' '-fopenmp' '-fopenmp-simd' '-funroll-loops' '-ftree-vectorize' '-Wno-register' '-D' 'LIBUS_NO_SSL' '-D' 'HAVE_INLINE' '-I' '/usr/include/postgresql' '-pthread' '-std=c++11' '-m64' '-I' '/home/chris/root/include' '-o' 'gaiawebql' '-L/home/chris/root/lib' '-pthread' '-rdynamic' '-L/usr/lib64' '-pthread' '-shared-libgcc' '-pthread'  
 /usr/lib64/gcc/x86_64-suse-linux/9/cc1plus -quiet -v -I /usr/include/postgresql -I /home/chris/root/include -D_GNU_SOURCE -D_REENTRANT -D LIBUS_NO_SSL -D HAVE_INLINE src/PJMCoords.cc -march=broadwell -mmmx -mno-3dnow -msse -msse2 -msse3 -mssse3 -mno-sse4a -mcx16 -msahf -mmovbe -maes -mno-sha -mpclmul -mpopcnt -mabm -mno-lwp -mfma -mno-fma4 -mno-xop -mbmi -mno-sgx -mbmi2 -mno-pconfig -mno-wbnoinvd -mno-tbm -mavx -mavx2 -msse4.2 -msse4.1 -mlzcnt -mrtm -mhle -mrdrnd -mf16c -mfsgsbase -mrdseed -mprfchw -madx -mfxsr -mxsave -mxsaveopt -mno-avx512f -mno-avx512er -mno-avx512cd -mno-avx512pf -mno-prefetchwt1 -mno-clflushopt -mno-xsavec -mno-xsaves -mno-avx512dq -mno-avx512bw -mno-avx512vl -mno-avx512ifma -mno-avx512vbmi -mno-avx5124fmaps -mno-avx5124vnniw -mno-clwb -mno-mwaitx -mno-clzero -mno-pku -mno-rdpid -mno-gfni -mno-shstk -mno-avx512vbmi2 -mno-avx512vnni -mno-vaes -mno-vpclmulqdq -mno-avx512bitalg -mno-movdiri -mno-movdir64b -mno-waitpkg -mno-cldemote -mno-ptwrite --param l1-cache-size=32 --param l1-cache-line-size=64 --param l2-cache-size=15360 -mtune=broadwell -quiet -dumpbase PJMCoords.cc -m64 -auxbase PJMCoords -g -O3 -Wno-register -std=c++17 -std=c++11 -version -fopenmp -fopenmp-simd -funroll-loops -ftree-vectorize -o /tmp/ccem2p3h.s  
GNU C++11 (SUSE Linux) version 9.2.1 20190903 [gcc-9-branch revision 275330] (x86_64-suse-linux)  
        compiled by GNU C version 9.2.1 20190903 [gcc-9-branch revision 275330], GMP version 6.1.2, MPFR version 4.0.2, MPC version 1.1.0, isl version isl-0.20-GMP  
  
GGC heuristics: --param ggc-min-expand=100 --param ggc-min-heapsize=131072  
ignoring nonexistent directory "/usr/include/postgresql"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/ipp/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/mkl/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/pstl/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include"  
ignoring duplicate directory "/opt/intel/compilers_and_libraries_2019.5.281/linux/daal/include"  
#include "..." search starts here:  
#include <...> search starts here:  
 /home/chris/root/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/ipp/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/mkl/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/pstl/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/include  
 /opt/intel/compilers_and_libraries_2019.5.281/linux/daal/include  
 /usr/include/c++/9  
 /usr/include/c++/9/x86_64-suse-linux  
 /usr/include/c++/9/backward  
 /usr/lib64/gcc/x86_64-suse-linux/9/include  
 /usr/local/include  
 /usr/lib64/gcc/x86_64-suse-linux/9/include-fixed  
 /usr/lib64/gcc/x86_64-suse-linux/9/../../../../x86_64-suse-linux/include  
 /usr/include  
End of search list.  
GNU C++11 (SUSE Linux) version 9.2.1 20190903 [gcc-9-branch revision 275330] (x86_64-suse-linux)  
        compiled by GNU C version 9.2.1 20190903 [gcc-9-branch revision 275330], GMP version 6.1.2, MPFR version 4.0.2, MPC version 1.1.0, isl version isl-0.20-GMP  
  
GGC heuristics: --param ggc-min-expand=100 --param ggc-min-heapsize=131072  
Compiler executable checksum: 00000000000000000000000000000000  
COLLECT_GCC_OPTIONS='-v' '-march=native' '-g' '-O3' '-std=c++17' '-fopenmp' '-fopenmp-simd' '-funroll-loops' '-ftree-vectorize' '-Wno-register' '-D' 'LIBUS_NO_SSL' '-D' 'HAVE_INLINE' '-I' '/usr/include/postgresql' '-pthread' '-std=c++11' '-m64' '-I' '/home/chris/root/include' '-o' 'gaiawebql' '-L/home/chris/root/lib' '-pthread' '-rdynamic' '-L/usr/lib64' '-pthread' '-shared-libgcc' '-pthread'  
 /usr/lib64/gcc/x86_64-suse-linux/9/../../../../x86_64-suse-linux/bin/as -v -I /usr/include/postgresql -I /home/chris/root/include --64 -o /tmp/ccZXwVmg.o /tmp/ccem2p3h.s  
GNU アセンブラ バージョン 2.33.1 (x86_64-suse-linux)、BFD バージョン (GNU Binutils; openSUSE Tumbleweed) 2.33.1.20191023-2 を使用  
COMPILER_PATH=/usr/lib64/gcc/x86_64-suse-linux/9/:/usr/lib64/gcc/x86_64-suse-linux/9/:/usr/lib64/gcc/x86_64-suse-linux/:/usr/lib64/gcc/x86_64-suse-linux/9/:/usr/lib64/gcc/x86_64-suse-linux/:/usr/lib64/gcc/x86_64-suse-linux/9/../../../../x86_64-suse-linux/bin/  
LIBRARY_PATH=/usr/lib64/gcc/x86_64-suse-linux/9/:/usr/lib64/gcc/x86_64-suse-linux/9/../../../../lib64/:/lib/../lib64/:/usr/lib/../lib64/:/opt/intel/compilers_and_libraries_2019.5.281/linux/mpi/intel64/libfabric/lib/:/opt/intel/compilers_and_libraries_2019.5.281/linux/ipp/lib/intel64/:/opt/intel/compilers_and_libraries_2019.5.281/linux/compiler/lib/intel64_lin/:/opt/intel/compilers_and_libraries_2019.5.281/linux/mkl/lib/intel64_lin/:/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/lib/intel64/gcc4.7/:/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/lib/intel64/gcc4.7/:/opt/intel/compilers_and_libraries_2019.5.281/linux/daal/lib/intel64_lin/:/opt/intel/compilers_and_libraries_2019.5.281/linux/mpi/intel64/libfabric/lib/:/opt/intel/compilers_and_libraries_2019.5.281/linux/ipp/lib/intel64/:/opt/intel/compilers_and_libraries_2019.5.281/linux/compiler/lib/intel64_lin/:/opt/intel/compilers_and_libraries_2019.5.281/linux/mkl/lib/intel64_lin/:/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/lib/intel64/gcc4.7/:/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/lib/intel64/gcc4.7/:/opt/intel/compilers_and_libraries_2019.5.281/linux/daal/lib/intel64_lin/:/opt/intel/compilers_and_libraries_2019.5.281/linux/daal/../tbb/lib/intel64_lin/gcc4.4/:/usr/lib64/gcc/x86_64-suse-linux/9/../../../../x86_64-suse-linux/lib/:/usr/lib64/gcc/x86_64-suse-linux/9/../../../:/lib/:/usr/lib/  
Reading specs from /usr/lib64/gcc/x86_64-suse-linux/9/libgomp.spec  
COLLECT_GCC_OPTIONS='-v' '-march=native' '-g' '-O3' '-std=c++17' '-fopenmp' '-fopenmp-simd' '-funroll-loops' '-ftree-vectorize' '-Wno-register' '-D' 'LIBUS_NO_SSL' '-D' 'HAVE_INLINE' '-I' '/usr/include/postgresql' '-pthread' '-std=c++11' '-m64' '-I' '/home/chris/root/include' '-o' 'gaiawebql' '-L/home/chris/root/lib' '-pthread' '-rdynamic' '-L/usr/lib64' '-pthread' '-shared-libgcc' '-pthread'  
 /usr/lib64/gcc/x86_64-suse-linux/9/collect2 -plugin /usr/lib64/gcc/x86_64-suse-linux/9/liblto_plugin.so -plugin-opt=/usr/lib64/gcc/x86_64-suse-linux/9/lto-wrapper -plugin-opt=-fresolution=/tmp/cchofFeh.res -plugin-opt=-pass-through=-lgcc_s -plugin-opt=-pass-through=-lgcc -plugin-opt=-pass-through=-lpthread -plugin-opt=-pass-through=-lc -plugin-opt=-pass-through=-lgcc_s -plugin-opt=-pass-through=-lgcc --build-id --eh-frame-hdr -m elf_x86_64 -export-dynamic -dynamic-linker /lib64/ld-linux-x86-64.so.2 -o gaiawebql /usr/lib64/gcc/x86_64-suse-linux/9/../../../../lib64/crt1.o /usr/lib64/gcc/x86_64-suse-linux/9/../../../../lib64/crti.o /usr/lib64/gcc/x86_64-suse-linux/9/crtbegin.o /usr/lib64/gcc/x86_64-suse-linux/9/crtoffloadbegin.o -L/home/chris/root/lib -L/usr/lib64 -L/usr/lib64/gcc/x86_64-suse-linux/9 -L/usr/lib64/gcc/x86_64-suse-linux/9/../../../../lib64 -L/lib/../lib64 -L/usr/lib/../lib64 -L/opt/intel/compilers_and_libraries_2019.5.281/linux/mpi/intel64/libfabric/lib -L/opt/intel/compilers_and_libraries_2019.5.281/linux/ipp/lib/intel64 -L/opt/intel/compilers_and_libraries_2019.5.281/linux/compiler/lib/intel64_lin -L/opt/intel/compilers_and_libraries_2019.5.281/linux/mkl/lib/intel64_lin -L/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/lib/intel64/gcc4.7 -L/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/lib/intel64/gcc4.7 -L/opt/intel/compilers_and_libraries_2019.5.281/linux/daal/lib/intel64_lin -L/opt/intel/compilers_and_libraries_2019.5.281/linux/mpi/intel64/libfabric/lib -L/opt/intel/compilers_and_libraries_2019.5.281/linux/ipp/lib/intel64 -L/opt/intel/compilers_and_libraries_2019.5.281/linux/compiler/lib/intel64_lin -L/opt/intel/compilers_and_libraries_2019.5.281/linux/mkl/lib/intel64_lin -L/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/lib/intel64/gcc4.7 -L/opt/intel/compilers_and_libraries_2019.5.281/linux/tbb/lib/intel64/gcc4.7 -L/opt/intel/compilers_and_libraries_2019.5.281/linux/daal/lib/intel64_lin -L/opt/intel/compilers_and_libraries_2019.5.281/linux/daal/../tbb/lib/intel64_lin/gcc4.4 -L/usr/lib64/gcc/x86_64-suse-linux/9/../../../../x86_64-suse-linux/lib -L/usr/lib64/gcc/x86_64-suse-linux/9/../../.. /tmp/cc08wUef.o /tmp/ccuAj3lf.o /tmp/ccEVG6gg.o /tmp/ccZXwVmg.o -lstdc++fs -lpq -luWS -lcurl -lcrypto -lssl -lz -l:libnuma.so.1 -lpthread -lCore -lImt -lRIO -lNet -lHist -lGraf -lGraf3d -lGpad -lROOTVecOps -lTree -lTreePlayer -lRint -lPostscript -lMatrix -lPhysics -lMathCore -lThread -lMultiProc -lROOTDataFrame -ldl -rpath /usr/lib64 -l:libjemalloc.so.2 -lm -lgcc -lm -ldl -lstdc++ -lm -lgomp -lgcc_s -lgcc -lpthread -lc -lgcc_s -lgcc /usr/lib64/gcc/x86_64-suse-linux/9/crtend.o /usr/lib64/gcc/x86_64-suse-linux/9/../../../../lib64/crtn.o /usr/lib64/gcc/x86_64-suse-linux/9/crtoffloadend.o  
COLLECT_GCC_OPTIONS='-v' '-march=native' '-g' '-O3' '-std=c++17' '-fopenmp' '-fopenmp-simd' '-funroll-loops' '-ftree-vectorize' '-Wno-register' '-D' 'LIBUS_NO_SSL' '-D' 'HAVE_INLINE' '-I' '/usr/include/postgresql' '-pthread' '-std=c++11' '-m64' '-I' '/home/chris/root/include' '-o' 'gaiawebql' '-L/home/chris/root/lib' '-pthread' '-rdynamic' '-L/usr/lib64' '-pthread' '-shared-libgcc' '-pthread'

---

## Comment 6

> Username: HDembinski  
> Created at: 2019-12-13 13:26:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/259#issuecomment-565440284  

> You were right. Indeed something else is injecting C++11 right after my C++17 flag! The code compiled OK this time because the boost/histogram header had been commented out.  
  
> "-g -O3 -Wno-register -std=c++17 -std=c++11 -version -fopenmp -fopenmp-simd -funroll-loops -ftree-vectorize"  
  
Later options override earlier options, so `-std=c++17 -std=c++11` means you are compiling with `-std=c++11`. Like Henry said, this is the reason why you see these errors. Boost.Histogram compiles fine on c++17 and c++20a. I don't know what is injecting this flag, but it is probably not this library.  
  
You can try out Boost.Histogram on [Wandbox](https://wandbox.org/), for example.  
  
And yes, you need to match this library with the rest of Boost, since Boost.Histogram depends on a few other Boost libraries. Sometimes we move things around like `nvp.hpp`. You wouldn't notice this when you use Boost.Histogram from your Boost installation.  
  
Speaking of which, perhaps it is easiest if you start with Boost.Histogram included in Boost-1.71, since you have that already.

---

## Comment 7

> Username: jvo203  
> Created at: 2019-12-13 13:37:44 UTC  
> Url: https://github.com/boostorg/histogram/issues/259#issuecomment-565443753  

Thanks to everyone for the answers/hints. OK, I will close this thread since there is nothing inherently wrong with boost/histogram itself. Will try including only boost/histogram at first, compiling and then gradually adding all the other libraries until the culprit responsible for injecting C++11 is found.

---

## Comment 8

> Username: chrisburr  
> Created at: 2019-12-13 14:05:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/259#issuecomment-565452637  

It's almost certainly ROOT via `/home/chris/root/bin/root-config --cflags`. You might need to rebuild ROOT or install from a source with C++17 mode enabled.

---

## Comment 9

> Username: HDembinski  
> Created at: 2019-12-13 14:43:43 UTC  
> Updated at: 2019-12-13 14:45:28 UTC  
> Url: https://github.com/boostorg/histogram/issues/259#issuecomment-565466070  

@jvo203 Thank you for reporting, even though this was not a bug. I hope you don't find a real one, but if you do, please continue reporting back. :)

---

## Comment 10

> Username: HDembinski  
> Created at: 2019-12-13 14:46:52 UTC  
> Url: https://github.com/boostorg/histogram/issues/259#issuecomment-565467233  

If you have control over the build system, you could try and append your `-std=c++17` flag after the `-std=c++11` flag... or filter out the former from `root-config --cflags`

---

## Comment 11

> Username: henryiii  
> Created at: 2019-12-13 15:39:26 UTC  
> Url: https://github.com/boostorg/histogram/issues/259#issuecomment-565487567  

If this is ROOT, you usually can't compile against it unless you exactly match the C++ standard flag originally used to build ROOT - so a rebuild or new package is in order. (Mostly due to poor decisions about injecting things into the standard namespace, I believe...) If it's something else, it's usually okay to set the highest flag needed (and is usually just a problem with their CMakeLists not using the CMake tools to set the standard, which plays nicely between packages, but instead injecting the standard flags themselves so they can support a 10+ year old version of CMake....).

---

## Comment 12

> Username: jvo203  
> Created at: 2019-12-14 02:57:48 UTC  
> Url: https://github.com/boostorg/histogram/issues/259#issuecomment-565675079  

A local CERN ROOT built from scratch with default settings indeed injects C++11:  
root-config --cflags  
-pthread -std=c++11 -m64 -I/home/chris/root/include  
  
So during the transitional period the solution would be, as per the threads above, to rebuild ROOT with C++17 and use both ROOT and boost/histogram. Alternatively don't bother with ROOT anymore, rip it out and switch 100% to boost/histogram, which has been the final goal since the beginning of this "exercise" anyway.  
  
Many thanks!

---

## Comment 13

> Username: jvo203  
> Created at: 2019-12-16 01:53:00 UTC  
> Url: https://github.com/boostorg/histogram/issues/259#issuecomment-565874360  

Recompiled ROOT with -Dcxx17=ON  
root-config --cflags  
-pthread -std=c++17 -m64 -I/home/chris/root/include  
  
Uncommented #include <boost/histogram.hpp>, the code compiles OK. ROOT and boost/histogram co-exist OK.
