# #388 - trying to install pcl 1.9.1 from source in Ubuntu 22.04 throws boost error --                 from /home/mona/Downloads/pcl-pcl-1.9.1/io/src/ply/ply_parser.cpp:41: /usr/local/include/boost/thread/pthread/thread_data.hpp:60:5: error: missing binary operator before token "("    60 | #if PTHREAD_STACK_MIN > 0 [Closed]

> Username: monajalal  
> Created at: 2023-06-05 18:58:55 UTC  
> Updated at: 2023-06-06 20:34:42 UTC  
> Closed at: 2023-06-06 20:34:42 UTC  
> Url: https://github.com/boostorg/thread/issues/388  

I get this boost error when I do `make` on Ubuntu 22.04.  
```  
(base) mona@ard-gpu-01:~/Downloads/boost_1_72_0$ sudo apt-get install build-essential g++ python3-dev autotools-dev libicu-dev libbz2-dev libboost-all-dev  
Reading package lists... Done  
Building dependency tree... Done  
Reading state information... Done  
autotools-dev is already the newest version (20220109.1).  
autotools-dev set to manually installed.  
build-essential is already the newest version (12.9ubuntu3).  
g++ is already the newest version (4:11.2.0-1ubuntu1).  
libicu-dev is already the newest version (70.1-2).  
libicu-dev set to manually installed.  
python3-dev is already the newest version (3.10.6-1~22.04).  
The following packages were automatically installed and are no longer required:  
  libaom-dev libarmadillo-dev libarpack2-dev libblosc-dev libbrotli-dev libceres2 libcfitsio-dev libcfitsio-doc libcharls-dev libdav1d-dev libde265-dev libdouble-conversion-dev libflann1.9 libfontconfig-dev libfontconfig1-dev libfreetype-dev libfreetype6-dev libfreexl-dev libfyba-dev libgeos-dev libgeotiff-dev libgif-dev libgl2ps-dev libhdf5-mpi-dev libhdf5-openmpi-103-1  
  libhdf5-openmpi-cpp-103-1 libhdf5-openmpi-dev libhdf5-openmpi-fortran-102 libhdf5-openmpi-hl-100 libhdf5-openmpi-hl-cpp-100 libhdf5-openmpi-hl-fortran-100 libheif-dev libjson-c-dev libjsoncpp-dev libkmlconvenience1 libkmlregionator1 libkmlxsd1 liblz4-dev libminizip-dev libnetcdf-c++4 libnetcdf-cxx-legacy-dev libogdi-dev libogg-dev libopenjp2-7-dev libopenni-dev  
  libopenni-sensor-pointclouds0 libopenni0 libopenni2-0 libopenni2-dev libpcl-people1.12 libpcre2-dev libpcre2-posix3 libpoppler-dev libpoppler-private-dev libproj-dev libqhull-dev libqhullcpp8.0 libqt5quickparticles5 libqt5quickshapes5 libqt5svg5-dev libqt5webkit5-dev librttopo-dev libspatialite-dev libtheora-dev liburiparser-dev libusb-1.0-0-dev libusb-1.0-doc libutfcpp-dev  
  libvtk9-java libvtk9.1-qt libwebp-dev libx265-dev libxerces-c-dev libxft-dev libxrender-dev libxss-dev openni-utils python3-mpi4py python3-vtk9 qtdeclarative5-dev qttools5-dev qttools5-private-dev tcl-dev tcl8.6-dev tk-dev tk8.6-dev uuid-dev vtk9  
Use 'sudo apt autoremove' to remove them.  
The following additional packages will be installed:  
  bzip2-doc libboost-dev  
Suggested packages:  
  libboost-doc  
The following NEW packages will be installed:  
  bzip2-doc libboost-all-dev libboost-dev libbz2-dev  
0 upgraded, 4 newly installed, 0 to remove and 5 not upgraded.  
Need to get 538 kB of archives.  
After this operation, 740 kB of additional disk space will be used.  
Do you want to continue? [Y/n] y  
Get:1 http://us.archive.ubuntu.com/ubuntu jammy/main amd64 bzip2-doc all 1.0.8-5build1 [500 kB]  
Get:2 http://us.archive.ubuntu.com/ubuntu jammy/main amd64 libboost-dev amd64 1.74.0.3ubuntu7 [3,490 B]  
Get:3 http://us.archive.ubuntu.com/ubuntu jammy/universe amd64 libboost-all-dev amd64 1.74.0.3ubuntu7 [2,268 B]  
Get:4 http://us.archive.ubuntu.com/ubuntu jammy/main amd64 libbz2-dev amd64 1.0.8-5build1 [32.5 kB]  
Fetched 538 kB in 0s (1,447 kB/s)      
Selecting previously unselected package bzip2-doc.  
(Reading database ... 420097 files and directories currently installed.)  
Preparing to unpack .../bzip2-doc_1.0.8-5build1_all.deb ...  
Unpacking bzip2-doc (1.0.8-5build1) ...  
Selecting previously unselected package libboost-dev:amd64.  
Preparing to unpack .../libboost-dev_1.74.0.3ubuntu7_amd64.deb ...  
Unpacking libboost-dev:amd64 (1.74.0.3ubuntu7) ...  
Selecting previously unselected package libboost-all-dev.  
Preparing to unpack .../libboost-all-dev_1.74.0.3ubuntu7_amd64.deb ...  
Unpacking libboost-all-dev (1.74.0.3ubuntu7) ...  
Selecting previously unselected package libbz2-dev:amd64.  
Preparing to unpack .../libbz2-dev_1.0.8-5build1_amd64.deb ...  
Unpacking libbz2-dev:amd64 (1.0.8-5build1) ...  
Setting up bzip2-doc (1.0.8-5build1) ...  
Setting up libboost-dev:amd64 (1.74.0.3ubuntu7) ...  
Setting up libbz2-dev:amd64 (1.0.8-5build1) ...  
Setting up libboost-all-dev (1.74.0.3ubuntu7) ...  
Processing triggers for install-info (6.8-4build1) ...  
(base) mona@ard-gpu-01:~/Downloads/boost_1_72_0$ cd ../pcl-pcl-1.9.1/  
(base) mona@ard-gpu-01:~/Downloads/pcl-pcl-1.9.1$  make -j8  
make: *** No targets specified and no makefile found.  Stop.  
(base) mona@ard-gpu-01:~/Downloads/pcl-pcl-1.9.1$ cd build/  
(base) mona@ard-gpu-01:~/Downloads/pcl-pcl-1.9.1/build$ cd ../pcl-pcl-1.9.1/  
bash: cd: ../pcl-pcl-1.9.1/: No such file or directory  
(base) mona@ard-gpu-01:~/Downloads/pcl-pcl-1.9.1/build$ make -j4  
Consolidate compiler generated dependencies of target pcl_pcd_convert_NaN_nan  
Consolidate compiler generated dependencies of target pcl_common  
[  0%] Building CXX object io/CMakeFiles/pcl_io_ply.dir/src/ply/ply_parser.cpp.o  
[  1%] Built target pcl_pcd_convert_NaN_nan  
cc1plus: warning: ‘-Wabi’ won’t warn about anything [-Wabi]  
cc1plus: note: ‘-Wabi’ warns about differences from the most up-to-date ABI, which is also used by default  
cc1plus: note: use e.g. ‘-Wabi=11’ to warn about changes from GCC 7  
[  2%] Building CXX object common/CMakeFiles/pcl_common.dir/src/io.cpp.o  
[  2%] Building CXX object common/CMakeFiles/pcl_common.dir/src/point_types.cpp.o  
[  2%] Building CXX object common/CMakeFiles/pcl_common.dir/src/pcl_base.cpp.o  
cc1plus: warning: ‘-Wabi’ won’t warn about anything [-Wabi]  
cc1plus: note: ‘-Wabi’ warns about differences from the most up-to-date ABI, which is also used by default  
cc1plus: note: use e.g. ‘-Wabi=11’ to warn about changes from GCC 7  
cc1plus: warning: ‘-Wabi’ won’t warn about anything [-Wabi]  
cc1plus: warning: ‘-Wabi’ won’t warn about anything [-Wabi]  
cc1plus: note: ‘-Wabi’ warns about differences from the most up-to-date ABI, which is also used by default  
cc1plus: note: ‘-Wabi’ warns about differences from the most up-to-date ABI, which is also used by default  
cc1plus: note: use e.g. ‘-Wabi=11’ to warn about changes from GCC 7  
cc1plus: note: use e.g. ‘-Wabi=11’ to warn about changes from GCC 7  
In file included from /usr/include/pthread.h:33,  
                 from /usr/include/x86_64-linux-gnu/c++/11/bits/gthr-default.h:35,  
                 from /usr/include/x86_64-linux-gnu/c++/11/bits/gthr.h:148,  
                 from /usr/include/c++/11/ext/atomicity.h:35,  
                 from /usr/include/c++/11/bits/ios_base.h:39,  
                 from /usr/include/c++/11/ios:42,  
                 from /usr/include/c++/11/istream:38,  
                 from /usr/include/c++/11/fstream:38,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/io/include/pcl/io/ply/ply_parser.h:44,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/io/src/ply/ply_parser.cpp:41:  
/usr/local/include/boost/thread/pthread/thread_data.hpp:60:5: error: missing binary operator before token "("  
   60 | #if PTHREAD_STACK_MIN > 0  
      |     ^~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/eigen3/Eigen/Core:88,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/include/pcl/impl/point_types.hpp:46,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/include/pcl/point_types.h:359,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/src/point_types.cpp:37:  
/usr/local/include/eigen3/Eigen/src/Core/products/Parallelizer.h: In function ‘void Eigen::internal::parallelize_gemm(const Functor&, Index, Index, Index, bool)’:  
/usr/local/include/eigen3/Eigen/src/Core/products/Parallelizer.h:162:19: error: ‘eigen_assert_exception’ is not a member of ‘Eigen’  
  162 |   if (errorCount) EIGEN_THROW_X(Eigen::eigen_assert_exception());  
      |                   ^~~~~~~~~~~~~  
In file included from /usr/local/include/eigen3/Eigen/Core:88,  
                 from /usr/local/include/eigen3/Eigen/StdVector:14,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/include/pcl/pcl_base.h:49,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/include/pcl/impl/pcl_base.hpp:40,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/src/pcl_base.cpp:39:  
/usr/local/include/eigen3/Eigen/src/Core/products/Parallelizer.h: In function ‘void Eigen::internal::parallelize_gemm(const Functor&, Index, Index, Index, bool)’:  
/usr/local/include/eigen3/Eigen/src/Core/products/Parallelizer.h:162:19: error: ‘eigen_assert_exception’ is not a member of ‘Eigen’  
  162 |   if (errorCount) EIGEN_THROW_X(Eigen::eigen_assert_exception());  
      |                   ^~~~~~~~~~~~~  
In file included from /usr/local/include/eigen3/Eigen/Core:88,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/include/pcl/impl/point_types.hpp:46,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/include/pcl/point_types.h:359,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/src/io.cpp:41:  
/usr/local/include/eigen3/Eigen/src/Core/products/Parallelizer.h: In function ‘void Eigen::internal::parallelize_gemm(const Functor&, Index, Index, Index, bool)’:  
/usr/local/include/eigen3/Eigen/src/Core/products/Parallelizer.h:162:19: error: ‘eigen_assert_exception’ is not a member of ‘Eigen’  
  162 |   if (errorCount) EIGEN_THROW_X(Eigen::eigen_assert_exception());  
      |                   ^~~~~~~~~~~~~  
In file included from /usr/local/include/boost/detail/endian.hpp:9,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/include/pcl/PCLPointCloud2.h:11,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/include/pcl/pcl_base.h:55,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/include/pcl/impl/pcl_base.hpp:40,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/src/pcl_base.cpp:39:  
/usr/local/include/boost/predef/detail/endian_compat.h: At global scope:  
/usr/local/include/boost/predef/detail/endian_compat.h:11:161: note: ‘#pragma message: The use of BOOST_*_ENDIAN and BOOST_BYTE_ORDER is deprecated. Please include <boost/predef/other/endian.h> and use BOOST_ENDIAN_*_BYTE instead’  
   11 | #pragma message("The use of BOOST_*_ENDIAN and BOOST_BYTE_ORDER is deprecated. Please include <boost/predef/other/endian.h> and use BOOST_ENDIAN_*_BYTE instead")  
      |                                                                                                                                                                 ^  
In file included from /usr/local/include/boost/detail/endian.hpp:9,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/io/include/pcl/io/ply/byte_order.h:43,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/io/include/pcl/io/ply/ply.h:45,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/io/include/pcl/io/ply/ply_parser.h:66,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/io/src/ply/ply_parser.cpp:41:  
/usr/local/include/boost/predef/detail/endian_compat.h:11:161: note: ‘#pragma message: The use of BOOST_*_ENDIAN and BOOST_BYTE_ORDER is deprecated. Please include <boost/predef/other/endian.h> and use BOOST_ENDIAN_*_BYTE instead’  
   11 | #pragma message("The use of BOOST_*_ENDIAN and BOOST_BYTE_ORDER is deprecated. Please include <boost/predef/other/endian.h> and use BOOST_ENDIAN_*_BYTE instead")  
      |                                                                                                                                                                 ^  
make[2]: *** [common/CMakeFiles/pcl_common.dir/build.make:76: common/CMakeFiles/pcl_common.dir/src/point_types.cpp.o] Error 1  
make[2]: *** Waiting for unfinished jobs....  
In file included from /usr/local/include/boost/detail/endian.hpp:9,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/include/pcl/PCLPointCloud2.h:11,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/include/pcl/pcl_base.h:55,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/include/pcl/common/io.h:45,  
                 from /home/mona/Downloads/pcl-pcl-1.9.1/common/src/io.cpp:42:  
/usr/local/include/boost/predef/detail/endian_compat.h: At global scope:  
/usr/local/include/boost/predef/detail/endian_compat.h:11:161: note: ‘#pragma message: The use of BOOST_*_ENDIAN and BOOST_BYTE_ORDER is deprecated. Please include <boost/predef/other/endian.h> and use BOOST_ENDIAN_*_BYTE instead’  
   11 | #pragma message("The use of BOOST_*_ENDIAN and BOOST_BYTE_ORDER is deprecated. Please include <boost/predef/other/endian.h> and use BOOST_ENDIAN_*_BYTE instead")  
      |                                                                                                                                                                 ^  
make[2]: *** [common/CMakeFiles/pcl_common.dir/build.make:104: common/CMakeFiles/pcl_common.dir/src/io.cpp.o] Error 1  
make[2]: *** [common/CMakeFiles/pcl_common.dir/build.make:90: common/CMakeFiles/pcl_common.dir/src/pcl_base.cpp.o] Error 1  
make[1]: *** [CMakeFiles/Makefile2:836: common/CMakeFiles/pcl_common.dir/all] Error 2  
make[1]: *** Waiting for unfinished jobs....  
make[2]: *** [io/CMakeFiles/pcl_io_ply.dir/build.make:76: io/CMakeFiles/pcl_io_ply.dir/src/ply/ply_parser.cpp.o] Error 1  
make[1]: *** [CMakeFiles/Makefile2:998: io/CMakeFiles/pcl_io_ply.dir/all] Error 2  
make: *** [Makefile:166: all] Error 2  
```
