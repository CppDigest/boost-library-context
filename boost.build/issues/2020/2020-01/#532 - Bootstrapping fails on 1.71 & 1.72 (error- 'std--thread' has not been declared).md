# #532 - Bootstrapping fails on 1.71 & 1.72 (error: 'std::thread' has not been declared) [Closed]

> Username: jungletek  
> Created at: 2020-01-30 08:43:04 UTC  
> Updated at: 2021-02-04 02:13:07 UTC  
> Closed at: 2021-02-04 02:13:07 UTC  
> Url: https://github.com/boostorg/build/issues/532  

```  
# bootstrap.bat gcc  
Building Boost.Build engine  
sysinfo.cpp: In function 'unsigned int {anonymous}::std_thread_hardware_concurrency()':  
sysinfo.cpp:93:21: error: 'std::thread' has not been declared  
         return std::thread::hardware_concurrency();  
                     ^~~~~~  
File Not Found  
  
Failed to build Boost.Build engine.  
Please consult bootstrap.log for further diagnostics.  
```  
I'm on Windows 7 x64, using GCC (x86_64-win32-sjlj-rev0, Built by MinGW-W64 project) 8.1.0.  
  
FWIW, I can still build the libraries without issue (using a version of b2 bootstrapped with 1.70, though it throws 'version mismatch' warnings).

---

## Comment 1

> Username: gpascualg  
> Created at: 2020-06-02 23:17:25 UTC  
> Updated at: 2020-06-02 23:53:12 UTC  
> Url: https://github.com/boostorg/build/issues/532#issuecomment-637856393  

I can confirm a similar error while trying to build with clang 11 and libc++:  
  
    /tmp/check_cxx11-7de210.o: In function `main':  
    check_cxx11.cpp:(.text+0x9): undefined reference to `std::thread::hardware_concurrency()'  
    clang-11: error: linker command failed with exit code 1 (use -v to see invocation)  
  
Edit: I was missing to link with -lc++, which I had to hardcode into the build.sh script

---

## Comment 2

> Username: grafikrobot  
> Created at: 2020-06-03 01:45:30 UTC  
> Url: https://github.com/boostorg/build/issues/532#issuecomment-637904236  

@gpascualg can you post the exact bootstrap command and possibly environment you used for buiding?

---

## Comment 3

> Username: gpascualg  
> Created at: 2020-06-03 01:54:26 UTC  
> Updated at: 2020-06-13 14:37:47 UTC  
> Url: https://github.com/boostorg/build/issues/532#issuecomment-637906488  

I actually have it all in a Docker, so basically it reduces to this:  
  
```  
FROM ubuntu:18.04  
  
RUN apt-get update && \  
        apt-get install -y \  
                git \  
                clang \  
                ninja-build \  
                wget \  
                libcurl4-openssl-dev \  
                zlib1g-dev \  
                libunwind8 \  
                libunwind8-dev \  
                gettext \  
                libicu-dev \  
                liblttng-ust-dev \  
                libssl-dev \  
                libkrb5-dev \  
                libnuma-dev  
  
RUN wget https://github.com/Kitware/CMake/releases/download/v3.16.4/cmake-3.16.4-Linux-x86_64.sh && \  
        chmod +x cmake-3.16.4-Linux-x86_64.sh && \  
        ./cmake-3.16.4-Linux-x86_64.sh --skip-license  
  
ADD build-llvmproject.sh /opt/build-llvmproject.sh  
RUN /opt/build-llvmproject.sh && ldconfig  
  
ENV CC=clang-11  
ENV CXX=clang-11  
ENV CXXFLAGS=-stdlib=libc++  
  
RUN cd /opt && \  
        wget https://dl.bintray.com/boostorg/release/1.73.0/source/boost_1_73_0.tar.bz2 && \  
        tar xf boost_1_73_0.tar.bz2  
  
RUN update-alternatives --install /usr/bin/clang++ clang++ /usr/bin/clang-11 100 && \  
        update-alternatives --install /usr/bin/clang clang /usr/bin/clang-11 100 && \  
        which clang++ && which clang  
  
RUN ls -al /usr/bin  
ENV NO_CXX11_CHECK=1  
RUN sed -i 's/echo_run \${B2_CXX} \${CXXFLAGS} \${B2_CXXFLAGS} \${B2_SOURCES} -o b2/echo_run \${B2_CXX} \${CXXFLAGS} \${B2_CXXFLAGS} \${B2_SOURCES} -o b2 -lc++abi -lc++/g' /opt/boost_1_73_0/tools/build/src/engine/build.sh  
RUN cd /opt/boost_1_73_0 && \  
        (./bootstrap.sh --prefix=/usr --with-toolset=clang -without-libraries=python; cat bootstrap.log) && \  
        ./b2 toolset=clang cxxflags="-std=c++20 -stdlib=libc++" linkflags="-stdlib=libc++ -lc++abi -lc++" stage release -j16 threading=multi link=static && \  
        /b2 install toolset=clang cxxflags="-std=c++20 -stdlib=libc++" linkflags="-stdlib=libc++ -lc++abi -lc++" threading=multi link=static  
  
```  
  
I haven't tried not using libc++ (ie. removing CXXFLAGS), which I assume might work as is.  
I added `NO_CXX11_CHECK=1`, to skip the initial check. It could be reenabled given the build command is properly modified, which I did not. As for the rest of the b2 building process, I must at least add -lc++. I do so in an admittedly really ugly hack with the `sed`. It is not necessary to add `-lc++abi`.  
  
For reference, if needed, clang is built from source. The script invoked is:  
  
```  
export WORKSPACE_ROOT="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"  
export TOOLS_DIR="${WORKSPACE_ROOT}/tools"  
export LLVM_PROJECT="${TOOLS_DIR}/llvm-project"  
  
: "${LLVMPROJECT_REPO:=https://github.com/llvm/llvm-project.git}"  
: "${LLVMPROJECT_REF:=master}"  
  
: "${LLVM_INSTALL_PREFIX:=/usr}"  
  
: "${CXX:=clang++}"  
: "${CC:=clang}"  
  
if [ ! -d "${TOOLS_DIR}" ]; then  
  mkdir -p "${TOOLS_DIR}" || exit  
fi  
  
cd "${TOOLS_DIR}" || exit  
  
if [ ! -d llvm-project/.git ]; then  
  git clone --progress --verbose --depth=1 -b "$LLVMPROJECT_REF" -- "$LLVMPROJECT_REPO" llvm-project || exit  
else  
  (cd llvm-project &&  
   git remote set-url origin "$LLVMPROJECT_REPO" &&  
   git fetch origin "$LLVMPROJECT_REF" &&  
   git checkout FETCH_HEAD) || exit  
fi  
  
cd "$WORKSPACE_ROOT" || exit  
  
if [ ! -d build/clang ]; then  
  mkdir -p build/clang || exit  
fi  
  
# Build clang toolchain  
(mkdir -p build && \  
 cd build && \  
 cmake -G Ninja -DLLVM_ENABLE_PROJECTS="clang;libc;libcxxabi;libcxx;lldb;lld" -DLLDB_ENABLE_PYTHON=OFF -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_BUILD_TYPE=Release "${LLVM_PROJECT}/llvm" && \  
 ninja && \  
 ninja install) || exit  
  
```  
  
Edit: B2 commends where missing a few flags, but it is still unrelated

---

## Comment 4

> Username: Kojoley  
> Created at: 2020-06-14 16:50:57 UTC  
> Url: https://github.com/boostorg/build/issues/532#issuecomment-643792546  

@gpascualg your issue seems to be related to #591

---

## Comment 5

> Username: pavolmarkovic-serato  
> Created at: 2020-09-30 22:19:00 UTC  
> Updated at: 2020-09-30 22:19:11 UTC  
> Url: https://github.com/boostorg/build/issues/532#issuecomment-701676612  

This also fails with boost 1.74.0 when buildig boost with Xcode 9 or later and targetting earlier system versions.  
  
Add before bootstrapping:  
`export MACOSX_DEPLOYMENT_TARGET=10.8`  
  
The workaround fix is to patch `build.sh` by adding `-stdlib=libc++` to clang target settings

---

## Comment 6

> Username: grafikrobot  
> Created at: 2021-02-04 02:13:07 UTC  
> Url: https://github.com/boostorg/build/issues/532#issuecomment-772971634  

This was fixed a while ago (coming to the next, 1.76, Boost release) by invoking g++/clang++ instead of plain gcc/clang.
