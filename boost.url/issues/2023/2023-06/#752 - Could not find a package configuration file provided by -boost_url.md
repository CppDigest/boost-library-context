# #752 - Could not find a package configuration file provided by "boost_url" [Closed]

> Username: rc452860  
> Created at: 2023-06-07 12:11:21 UTC  
> Updated at: 2023-07-18 19:15:29 UTC  
> Closed at: 2023-07-18 19:15:29 UTC  
> Url: https://github.com/boostorg/url/issues/752  

```  
[root@ea7b5d759e4e build]# gcc -v   
Using built-in specs.  
COLLECT_GCC=gcc  
COLLECT_LTO_WRAPPER=/usr/libexec/gcc/x86_64-redhat-linux/4.8.5/lto-wrapper  
Target: x86_64-redhat-linux  
Configured with: ../configure --prefix=/usr --mandir=/usr/share/man --infodir=/usr/share/info --with-bugurl=http://bugzilla.redhat.com/bugzilla --enable-bootstrap --enable-shared --enable-threads=posix --enable-checking=release --with-system-zlib --enable-__cxa_atexit --disable-libunwind-exceptions --enable-gnu-unique-object --enable-linker-build-id --with-linker-hash-style=gnu --enable-languages=c,c++,objc,obj-c++,java,fortran,ada,go,lto --enable-plugin --enable-initfini-array --disable-libgcj --with-isl=/builddir/build/BUILD/gcc-4.8.5-20150702/obj-x86_64-redhat-linux/isl-install --with-cloog=/builddir/build/BUILD/gcc-4.8.5-20150702/obj-x86_64-redhat-linux/cloog-install --enable-gnu-indirect-function --with-tune=generic --with-arch_32=x86-64 --build=x86_64-redhat-linux  
Thread model: posix  
gcc version 4.8.5 20150623 (Red Hat 4.8.5-44) (GCC)  
```  
  
CmakeLists.txt:  
```  
set(Boost_USE_STATIC_LIBS ON)  
find_package(Boost REQUIRED COMPONENTS filesystem iostreams system thread chrono date_time url)  
```  
  
```  
[root@ea7b5d759e4e build]# cmake3 ..  
CMake Error at /usr/local/lib/cmake/Boost-1.81.0/BoostConfig.cmake:141 (find_package):  
  Could not find a package configuration file provided by "boost_url"  
  (requested version 1.81.0) with any of the following names:  
  
    boost_urlConfig.cmake  
    boost_url-config.cmake  
  
  Add the installation prefix of "boost_url" to CMAKE_PREFIX_PATH or set  
  "boost_url_DIR" to a directory containing one of the above files.  If  
  "boost_url" provides a separate development package or SDK, be sure it has  
  been installed.  
Call Stack (most recent call first):  
  /usr/local/lib/cmake/Boost-1.81.0/BoostConfig.cmake:262 (boost_find_component)  
  /usr/share/cmake3/Modules/FindBoost.cmake:444 (find_package)  
  CMakeLists.txt:9 (find_package)  
  
  
-- Configuring incomplete, errors occurred!  
See also "/src/build/CMakeFiles/CMakeOutput.log".  
See also "/src/build/CMakeFiles/CMakeError.log".  
```  
  
Dockerfile:  
```  
FROM centos:7  
  
RUN uname -a  
  
ARG DEBIAN_FRONTEND=noninteractive  
ENV TZ=Asia/Shanghai  
  
RUN sed -e 's|^mirrorlist=|#mirrorlist=|g' \  
         -e 's|^#baseurl=http://mirror.centos.org/centos|baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos|g' \  
         -i.bak \  
         /etc/yum.repos.d/CentOS-*.repo  
  
RUN yum update -y && \  
    yum install -y epel-release && \  
    yum -y install https://mirrors.aliyun.com/ius/ius-release-el7.rpm  && \  
    yum install -y centos-release-scl && \  
    yum groupinstall -y "Development Tools" &&\  
    yum install -y which cmake3 make wget unzip curl libcurl-devel zlib-devel git-lfs python-devel python2-pip python3 python3-devel python3-pip xz  
  
# ENV PATH="/opt/rh/devtoolset-8/root/usr/bin:${PATH}"  
# RUN scl enable devtoolset-8  
  
WORKDIR /tmp  
RUN wget https://boostorg.jfrog.io/artifactory/main/release/1.81.0/source/boost_1_81_0.tar.gz  
RUN tar -xzf boost_1_81_0.tar.gz && \  
    cd boost_1_81_0 && \  
    ./bootstrap.sh --with-libraries=all && \  
    ./b2 link=static cxxflags="-fPIC" --with=all install && \  
    cd .. && rm -rf boost_1_81_0*  
  
```  
  
This error does not appear on gcc8.5.0.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2023-06-07 17:58:13 UTC  
> Updated at: 2023-06-07 17:59:28 UTC  
> Url: https://github.com/boostorg/url/issues/752#issuecomment-1581272787  

This is something we test regularly on all workflows:  
  
https://github.com/boostorg/url/blob/develop/test/cmake_test/CMakeLists.txt  
  
Could you check if this error is still in master?  
  
> This error does not appear on gcc8.5.0.  
  
This is very unlikely to be related to the compiler.

---

## Comment 2

> Username: rc452860  
> Created at: 2023-06-08 08:20:33 UTC  
> Url: https://github.com/boostorg/url/issues/752#issuecomment-1582122200  

To resolve the issue, I attempted to upgrade Boost to version 1.82.0. However, the problem remained unresolved. After adding -std=c++11 to the cxxflags and trying again, the issue was successfully resolved.  
  
Here is the final Dockerfile (snippet):  
```  
RUN wget https://boostorg.jfrog.io/artifactory/main/release/1.82.0/source/boost_1_82_0.tar.gz  
RUN tar -xzf boost_1_82_0.tar.gz && \  
    cd boost_1_82_0 && \  
    ./bootstrap.sh --with-libraries=all && \  
    ./b2 link=static cxxflags="-fPIC -std=c++11" --with=all install && \  
    cd .. && rm -rf boost_1_82_0*  
```
