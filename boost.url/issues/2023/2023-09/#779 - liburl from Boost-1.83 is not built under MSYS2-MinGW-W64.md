# #779 - liburl from Boost-1.83 is not built under MSYS2/MinGW-W64 [Closed]

> Username: jdx-gh  
> Created at: 2023-09-13 08:42:29 UTC  
> Updated at: 2023-12-21 18:31:08 UTC  
> Closed at: 2023-12-21 18:31:08 UTC  
> Url: https://github.com/boostorg/url/issues/779  

When I try to build Boost I get the following message:  
```  
[...]  
gcc.link.dll D:\Works\boost_1_83_0\builddir\boost\bin.v2\libs\url\build\gcc-13\release\threadapi-win32\threading-multi\visibility-hidden\libboost_url-mgw13-mt-x32-1_83.dll  
common.copy D:\Works\Libraries\boost-1.83.0\lib\libboost_url-mgw13-mt-x32-1_83.dll.a  
The system cannot find the file specified.  
  
    copy /b "D:\Works\boost_1_83_0\builddir\boost\bin.v2\libs\url\build\gcc-13\release\threadapi-win32\threading-multi\visibility-hidden\libboost_url-mgw13-mt-x32-1_83.dll.a" + this-file-does-not-exist-A698EE7806899E69 "D:\Works\Libraries\boost-1.83.0\lib\libboost_url-mgw13-mt-x32-1_83.dll.a"  
  
...failed common.copy D:\Works\Libraries\boost-1.83.0\lib\libboost_url-mgw13-mt-x32-1_83.dll.a...  
boost-install.generate-cmake-variant- D:\Works\boost_1_83_0\builddir\boost\bin.v2\libs\url\build\gcc-13\release\threadapi-win32\threading-multi\visibility-hidden\libboost_url-variant-mgw13-mt-x32-1_83-shared.cmake  
common.copy D:\Works\Libraries\boost-1.83.0\lib\cmake\boost_url-1.83.0\libboost_url-variant-mgw13-mt-x32-1_83-shared.cmake  
D:\Works\boost_1_83_0\builddir\boost\bin.v2\libs\url\build\gcc-13\release\threadapi-win32\threading-multi\visibility-hidden\libboost_url-variant-mgw13-mt-x32-1_83-shared.cmake  
        1 file(s) copied.  
common.copy D:\Works\Libraries\boost-1.83.0\lib\libboost_url-mgw13-mt-x32-1_83.dll  
The system cannot find the file specified.  
  
    copy /b "D:\Works\boost_1_83_0\builddir\boost\bin.v2\libs\url\build\gcc-13\release\threadapi-win32\threading-multi\visibility-hidden\libboost_url-mgw13-mt-x32-1_83.dll" + this-file-does-not-exist-A698EE7806899E69 "D:\Works\Libraries\boost-1.83.0\lib\libboost_url-mgw13-mt-x32-1_83.dll"  
  
...failed common.copy D:\Works\Libraries\boost-1.83.0\lib\libboost_url-mgw13-mt-x32-1_83.dll...  
[...]  
```  
At the same time in the root source directory (where b2 is) files `Worksboost_1_83_0builddirboostbin.v2libsurlbuildgcc-13releasethreadapi-win32threading-multivisibility-hiddenlibboost_url-mgw13-mt-x32-1_83.dll.a` and `Worksboost_1_83_0builddirboostbin.v2libsurlbuildgcc-13releasethreadapi-win32threading-multivisibility-hiddenlibboost_url-mgw13-mt-x32-1_83.dll` are created.  
  
It seems to be a forward slash/backslash issue – as far as I can see liburl is the only library which uses response files (.rsp) and generated response files use both types of slashes, while, I think, they should use only forward slashes.   
  
Static version has similar problem:  
```  
[...]  
gcc.archive D:\Works\boost_1_83_0\builddir\boost\bin.v2\libs\url\build\gcc-13\release\link-static\threadapi-win32\threading-multi\visibility-hidden\libboost_url-mgw13-mt-x32-1_83.a  
C:/Tools/MSYS/mingw32/i686-w64-mingw32/bin/ar.exe: D:Worksboost_1_83_0builddirboostbin.v2libsurlbuildgcc-13releaselink-staticthreadapi-win32threading-multivisibility-hiddenauthority_view.o: No such file or directory  
  
    "C:/Tools/MSYS/mingw32/i686-w64-mingw32/bin/ar.exe"  rsc "D:\Works\boost_1_83_0\builddir\boost\bin.v2\libs\url\build\gcc-13\release\link-static\threadapi-win32\threading-multi\visibility-hidden\libboost_url-mgw13-mt-x32-1_83.a" @"D:/Works/boost_1_83_0/builddir/boost/bin.v2/libs/url/build/gcc-13/release/link-static/threadapi-win32/threading-multi/visibility-hidden/libboost_url-mgw13-mt-x32-1_83.a.rsp"  
  
...failed gcc.archive D:\Works\boost_1_83_0\builddir\boost\bin.v2\libs\url\build\gcc-13\release\link-static\threadapi-win32\threading-multi\visibility-hidden\libboost_url-mgw13-mt-x32-1_83.a...  
...removing D:\Works\boost_1_83_0\builddir\boost\bin.v2\libs\url\build\gcc-13\release\link-static\threadapi-win32\threading-multi\visibility-hidden\libboost_url-mgw13-mt-x32-1_83.a  
...skipped <pD:\Works\Libraries\boost-1.83.0\lib>libboost_url-mgw13-mt-x32-1_83.a for lack of <pD:\Works\boost_1_83_0\builddir\boost\bin.v2\libs\url\build\gcc-13\release\link-static\threadapi-win32\threading-multi\visibility-hidden>libboost_url-mgw13-mt-x32-1_83.a...  
...skipped <pD:\Works\boost_1_83_0\builddir\boost\bin.v2\libs\url\build\gcc-13\release\link-static\threadapi-win32\threading-multi\visibility-hidden>libboost_url-variant-mgw13-mt-x32-1_83-static.cmake for lack of <pD:\Works\boost_1_83_0\builddir\boost\bin.v2\libs\url\build\gcc-13\release\link-static\threadapi-win32\threading-multi\visibility-hidden>libboost_url-mgw13-mt-x32-1_83.a...  
...skipped <pD:\Works\Libraries\boost-1.83.0\lib\cmake\boost_url-1.83.0>libboost_url-variant-mgw13-mt-x32-1_83-static.cmake for lack of <pD:\Works\boost_1_83_0\builddir\boost\bin.v2\libs\url\build\gcc-13\release\link-static\threadapi-win32\threading-multi\visibility-hidden>libboost_url-variant-mgw13-mt-x32-1_83-static.cmake...  
[...]  
```

---

## Comment 1

> Username: alandefreitas  
> Created at: 2023-09-13 16:19:12 UTC  
> Updated at: 2023-09-13 16:19:22 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1717940171  

What command are you using to build this? We do test [MinGW](https://github.com/boostorg/url/actions/runs/5869436667/job/15914460677) and also [shared libraries with MinGW](https://github.com/boostorg/url/actions/runs/5869436667/job/15914461414) and I've never seen this problem at the build system level. I'm not sure there's something we can do at the library level or its build scripts because they all use relative paths.

---

## Comment 2

> Username: jdx-gh  
> Created at: 2023-09-13 21:32:15 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1718344417  

In MSYS2 terminal:  
1. `./bootstrap.sh`  
2. `./b2 --prefix="/d/Works/Libraries/boost-1.83.0" --build-dir="/d/Works/boost_1_83_0/builddir"   
--build-type=complete --layout=versioned --without-mpi --without-python variant=release threading=multi   
address-model=32 toolset=gcc define=BOOST_USE_WINAPI_VERSION=0x0501 define=_WIN32_WINNT=0x501 install`

---

## Comment 3

> Username: atauzki  
> Created at: 2023-10-03 15:05:31 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1745170655  

Same problem with me. I compiled it with toolchains in winlibs.com.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2023-10-03 18:07:08 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1745474895  

Does the process work with cmake?

---

## Comment 5

> Username: atauzki  
> Created at: 2023-10-04 00:42:46 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1745955420  

with b2

---

## Comment 6

> Username: alandefreitas  
> Created at: 2023-10-04 00:45:26 UTC  
> Updated at: 2023-10-04 00:45:35 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1745957591  

Yes. I'm trying to figure out if it's a library issue or a b2 issue. It seems like a b2 issue because it's related to the use of relative paths.

---

## Comment 7

> Username: atauzki  
> Created at: 2023-10-04 03:52:47 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1746091670  

I tried cmake and it works. And yesterday I tried to execute the error command manually and replace the backslashes to slash in the rsp file, b2 can compile successfully.

---

## Comment 8

> Username: alandefreitas  
> Created at: 2023-10-04 05:42:14 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1746173309  

@grafikrobot is there anything we can or should do about it?

---

## Comment 9

> Username: babelvit  
> Created at: 2023-10-21 19:30:05 UTC  
> Updated at: 2023-10-21 19:33:32 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1773902231  

@alandefreitas I can confirm with certainty that the same error appears  
-  with boost/1.82 as well  
- with MinGW releases other than MSYS2; namely winlibs.com/mingw/[11.4 - 13.2]

---

## Comment 10

> Username: seppeon  
> Created at: 2023-12-07 03:00:14 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1844146535  

I also have this issue.

---

## Comment 11

> Username: pdimov  
> Created at: 2023-12-07 15:44:16 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1845580824  

Under Windows, you should build `b2` using `bootstrap.bat` instead of `bootstrap.sh` from a `bash` shell, in order for it to know that it's running under Windows so that it can interpret `\` as a directory separator.

---

## Comment 12

> Username: seppeon  
> Created at: 2023-12-16 11:55:07 UTC  
> Updated at: 2023-12-16 11:59:23 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1858799905  

I was using powershell, not a bash shell. However, I was using it as a conan package.

---

## Comment 13

> Username: pdimov  
> Created at: 2023-12-16 14:30:46 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1858832004  

This sounds like a b2 issue for me. However, looking at gcc.jam, it doesn't seem to be true that URL is the only library using response files. E.g. in  
  
https://github.com/bfgroup/b2/blob/5dca2965cb083060137498d6581fcbfb9522cfbc/src/tools/gcc.jam#L1094  
  
response files are used unconditionally.  
  
So this same issue should occur with any compiled Boost library, and it's odd if it indeed affects only URL.

---

## Comment 14

> Username: seppeon  
> Created at: 2023-12-17 01:51:08 UTC  
> Updated at: 2023-12-17 01:51:43 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1859011387  

This is my reproduction:  
```YAML  
[requires]  
boost/1.83.0  
  
[generators]  
CMakeDeps  
CMakeToolchain  
```  
  
On windows 10, with msys2, with the following packages:  
```  
asciidoc 10.2.0-3  
autoconf-wrapper 20221207-1  
autoconf2.13 2.13-6  
autoconf2.69 2.69-4  
autoconf2.71 2.71-3  
automake-wrapper 20221207-1  
automake1.11 1.11.6-6  
automake1.12 1.12.6-6  
automake1.13 1.13.4-7  
automake1.14 1.14.1-6  
automake1.15 1.15.1-4  
automake1.16 1.16.5-1  
autotools 2022.01.16-2  
base 2022.06-1  
bash 5.2.021-1  
bash-completion 2.11-3  
binutils 2.41-3  
bison 3.8.2-5  
brotli 1.1.0-1  
bsdtar 3.7.2-1  
btyacc 20230521-1  
bzip2 1.0.8-4  
ca-certificates 20230311-1  
clang 11.0.0-5  
coreutils 8.32-5  
curl 8.4.0-1  
dash 0.5.12-1  
db 5.3.28-5  
diffstat 1.65-1  
diffutils 3.10-1  
docbook-xml 4.5-4  
docbook-xsl 1.79.2-3  
dos2unix 7.5.1-1  
expat 2.5.0-1  
file 5.45-1  
filesystem 2023.02.07-1  
findutils 4.9.0-3  
flex 2.6.4-3  
gawk 5.3.0-1  
gcc 13.2.0-2  
gcc-libs 13.2.0-2  
gdb 13.2-1  
gdbm 1.23-1  
getent 2.18.90-4  
gettext 0.22.4-1  
gettext-devel 0.22.4-1  
glib2 2.76.6-1  
gmp 6.3.0-1  
gnupg 2.2.41-1  
gperf 3.1-6  
grep 1~3.0-6  
groff 1.23.0-2  
gzip 1.13-1  
heimdal-libs 7.8.0-4  
help2man 1.49.3-1  
icu 73.2-2  
inetutils 2.4-2  
info 7.1-1  
intltool 0.51.0-3  
isl 0.26-1  
less 643-1  
libargp 20110921-4  
libasprintf 0.22.4-1  
libassuan 2.5.6-1  
libbz2 1.0.8-4  
libcrypt 2.1-5  
libcurl 8.4.0-1  
libdb 5.3.28-5  
libedit 20221030_3.1-1  
libexpat 2.5.0-1  
libffi 3.4.4-1  
libgc 8.2.4-2  
libgcrypt 1.10.3-1  
libgdbm 1.23-1  
libgettextpo 0.22.4-1  
libgnutls 3.8.2-1  
libgpg-error 1.47-1  
libgpgme 1.23.1-1  
libguile 3.0.9-2  
libhogweed 3.9.1-1  
libiconv 1.17-1  
libiconv-devel 1.17-1  
libidn2 2.3.4-2  
libintl 0.22.4-1  
libksba 1.6.5-1  
libltdl 2.4.7-3  
liblz4 1.9.4-1  
liblzma 5.4.5-1  
libnettle 3.9.1-1  
libnghttp2 1.58.0-1  
libnpth 1.6-1  
libopenssl 3.1.4-1  
libp11-kit 0.25.3-1  
libpcre 8.45-4  
libpcre2_16 10.42-1  
libpcre2_32 10.42-1  
libpcre2_8 10.42-1  
libpcre2posix 10.42-1  
libpipeline 1.5.7-1  
libpsl 0.21.2-1  
libreadline 8.2.007-1  
libsqlite 3.44.1-1  
libssh2 1.11.0-1  
libtasn1 4.19.0-1  
libtool 2.4.7-3  
libunistring 1.1-2  
libutil-linux 2.35.2-4  
libxcrypt 4.4.36-1  
libxml2 2.12.1-1  
libxslt 1.1.39-1  
libxxhash 0.8.2-1  
libzstd 1.5.5-1  
llvm 11.0.0-5  
m4 1.4.19-2  
make 4.4.1-1  
man-db 2.11.2-1  
mingw-w64-clang-i686-bzip2 1.0.8-3  
mingw-w64-clang-i686-ca-certificates 20230311-1  
mingw-w64-clang-i686-crt-git 11.0.0.r404.g3a137bd87-1  
mingw-w64-clang-i686-expat 2.5.0-1  
mingw-w64-clang-i686-gdb 13.2-3  
mingw-w64-clang-i686-gettext 0.22.4-3  
mingw-w64-clang-i686-gmp 6.3.0-2  
mingw-w64-clang-i686-headers-git 11.0.0.r404.g3a137bd87-1  
mingw-w64-clang-i686-libc++ 17.0.4-1  
mingw-w64-clang-i686-libffi 3.4.4-1  
mingw-w64-clang-i686-libiconv 1.17-3  
mingw-w64-clang-i686-libsystre 1.0.1-4  
mingw-w64-clang-i686-libtasn1 4.19.0-1  
mingw-w64-clang-i686-libtre-git r128.6fb7206-2  
mingw-w64-clang-i686-libunwind 17.0.4-1  
mingw-w64-clang-i686-mpc 1.3.1-2  
mingw-w64-clang-i686-mpdecimal 2.5.1-1  
mingw-w64-clang-i686-mpfr 4.2.1-2  
mingw-w64-clang-i686-ncurses 6.4.20230708-1  
mingw-w64-clang-i686-openssl 3.2.0-1  
mingw-w64-clang-i686-p11-kit 0.25.3-1  
mingw-w64-clang-i686-python 3.11.6-2  
mingw-w64-clang-i686-readline 8.2.001-6  
mingw-w64-clang-i686-sqlite3 3.44.0-1  
mingw-w64-clang-i686-tcl 8.6.12-2  
mingw-w64-clang-i686-termcap 1.3.1-7  
mingw-w64-clang-i686-tk 8.6.12-2  
mingw-w64-clang-i686-tzdata 2023c-1  
mingw-w64-clang-i686-xxhash 0.8.2-1  
mingw-w64-clang-i686-xz 5.4.5-1  
mingw-w64-clang-i686-zlib 1.3-1  
mingw-w64-clang-i686-zstd 1.5.5-1  
mingw-w64-clang-x86_64-bzip2 1.0.8-3  
mingw-w64-clang-x86_64-ca-certificates 20230311-1  
mingw-w64-clang-x86_64-clang 17.0.4-1  
mingw-w64-clang-x86_64-clang-tools-extra 17.0.4-1  
mingw-w64-clang-x86_64-compiler-rt 17.0.4-1  
mingw-w64-clang-x86_64-crt-git 11.0.0.r404.g3a137bd87-1  
mingw-w64-clang-x86_64-expat 2.5.0-1  
mingw-w64-clang-x86_64-gdb 13.2-3  
mingw-w64-clang-x86_64-gettext 0.22.4-3  
mingw-w64-clang-x86_64-gmp 6.3.0-2  
mingw-w64-clang-x86_64-headers-git 11.0.0.r404.g3a137bd87-1  
mingw-w64-clang-x86_64-libc++ 17.0.4-1  
mingw-w64-clang-x86_64-libffi 3.4.4-1  
mingw-w64-clang-x86_64-libiconv 1.17-3  
mingw-w64-clang-x86_64-libsystre 1.0.1-4  
mingw-w64-clang-x86_64-libtasn1 4.19.0-1  
mingw-w64-clang-x86_64-libtre-git r128.6fb7206-2  
mingw-w64-clang-x86_64-libunwind 17.0.4-1  
mingw-w64-clang-x86_64-libwinpthread-git 11.0.0.r404.g3a137bd87-1  
mingw-w64-clang-x86_64-libxml2 2.12.1-1  
mingw-w64-clang-x86_64-lld 17.0.4-1  
mingw-w64-clang-x86_64-llvm 17.0.4-1  
mingw-w64-clang-x86_64-llvm-libs 17.0.4-1  
mingw-w64-clang-x86_64-mpc 1.3.1-2  
mingw-w64-clang-x86_64-mpdecimal 2.5.1-1  
mingw-w64-clang-x86_64-mpfr 4.2.1-2  
mingw-w64-clang-x86_64-ncurses 6.4.20230708-1  
mingw-w64-clang-x86_64-openssl 3.2.0-1  
mingw-w64-clang-x86_64-p11-kit 0.25.3-1  
mingw-w64-clang-x86_64-python 3.11.6-2  
mingw-w64-clang-x86_64-readline 8.2.001-6  
mingw-w64-clang-x86_64-sqlite3 3.44.0-1  
mingw-w64-clang-x86_64-tcl 8.6.12-2  
mingw-w64-clang-x86_64-termcap 1.3.1-7  
mingw-w64-clang-x86_64-tk 8.6.12-2  
mingw-w64-clang-x86_64-tzdata 2023c-1  
mingw-w64-clang-x86_64-winpthreads-git 11.0.0.r404.g3a137bd87-1  
mingw-w64-clang-x86_64-xxhash 0.8.2-1  
mingw-w64-clang-x86_64-xz 5.4.5-1  
mingw-w64-clang-x86_64-zlib 1.3-1  
mingw-w64-clang-x86_64-zstd 1.5.5-1  
mingw-w64-cross-clang 11.0.0-4  
mingw-w64-cross-clang-crt 9.0.0.6158.1c773877-2  
mingw-w64-cross-clang-headers 9.0.0.6158.1c773877-2  
mingw-w64-cross-compiler-rt 11.0.0-6  
mingw-w64-i686-bzip2 1.0.8-3  
mingw-w64-i686-ca-certificates 20230311-1  
mingw-w64-i686-expat 2.5.0-1  
mingw-w64-i686-gcc-libs 13.2.0-2  
mingw-w64-i686-gdb 13.2-3  
mingw-w64-i686-gettext 0.22.4-3  
mingw-w64-i686-gmp 6.3.0-2  
mingw-w64-i686-libffi 3.4.4-1  
mingw-w64-i686-libiconv 1.17-3  
mingw-w64-i686-libsystre 1.0.1-4  
mingw-w64-i686-libtasn1 4.19.0-1  
mingw-w64-i686-libtre-git r128.6fb7206-2  
mingw-w64-i686-libwinpthread-git 11.0.0.r404.g3a137bd87-1  
mingw-w64-i686-mpc 1.3.1-2  
mingw-w64-i686-mpdecimal 2.5.1-1  
mingw-w64-i686-mpfr 4.2.1-2  
mingw-w64-i686-ncurses 6.4.20230708-1  
mingw-w64-i686-openssl 3.2.0-1  
mingw-w64-i686-p11-kit 0.25.3-1  
mingw-w64-i686-python 3.11.6-2  
mingw-w64-i686-readline 8.2.001-6  
mingw-w64-i686-sqlite3 3.44.0-1  
mingw-w64-i686-tcl 8.6.12-2  
mingw-w64-i686-termcap 1.3.1-7  
mingw-w64-i686-tk 8.6.12-2  
mingw-w64-i686-tzdata 2023c-1  
mingw-w64-i686-xxhash 0.8.2-1  
mingw-w64-i686-xz 5.4.5-1  
mingw-w64-i686-zlib 1.3-1  
mingw-w64-i686-zstd 1.5.5-1  
mingw-w64-ucrt-x86_64-arm-none-eabi-binutils 2.41-2  
mingw-w64-ucrt-x86_64-arm-none-eabi-gdb 9.2-8  
mingw-w64-ucrt-x86_64-binutils 2.41-2  
mingw-w64-ucrt-x86_64-bzip2 1.0.8-3  
mingw-w64-ucrt-x86_64-ca-certificates 20230311-1  
mingw-w64-ucrt-x86_64-crt-git 11.0.0.r404.g3a137bd87-1  
mingw-w64-ucrt-x86_64-expat 2.5.0-1  
mingw-w64-ucrt-x86_64-gcc 13.2.0-2  
mingw-w64-ucrt-x86_64-gcc-libs 13.2.0-2  
mingw-w64-ucrt-x86_64-gettext 0.22.4-3  
mingw-w64-ucrt-x86_64-gmp 6.3.0-2  
mingw-w64-ucrt-x86_64-headers-git 11.0.0.r404.g3a137bd87-1  
mingw-w64-ucrt-x86_64-isl 0.26-1  
mingw-w64-ucrt-x86_64-libffi 3.4.4-1  
mingw-w64-ucrt-x86_64-libiconv 1.17-3  
mingw-w64-ucrt-x86_64-libsystre 1.0.1-4  
mingw-w64-ucrt-x86_64-libtasn1 4.19.0-1  
mingw-w64-ucrt-x86_64-libtre-git r128.6fb7206-2  
mingw-w64-ucrt-x86_64-libwinpthread-git 11.0.0.r404.g3a137bd87-1  
mingw-w64-ucrt-x86_64-libxml2 2.12.1-1  
mingw-w64-ucrt-x86_64-llvm 17.0.4-1  
mingw-w64-ucrt-x86_64-llvm-libs 17.0.4-1  
mingw-w64-ucrt-x86_64-mpc 1.3.1-2  
mingw-w64-ucrt-x86_64-mpdecimal 2.5.1-1  
mingw-w64-ucrt-x86_64-mpfr 4.2.1-2  
mingw-w64-ucrt-x86_64-ncurses 6.4.20230708-1  
mingw-w64-ucrt-x86_64-openssl 3.2.0-1  
mingw-w64-ucrt-x86_64-p11-kit 0.25.3-1  
mingw-w64-ucrt-x86_64-python 3.11.6-2  
mingw-w64-ucrt-x86_64-readline 8.2.001-6  
mingw-w64-ucrt-x86_64-sqlite3 3.44.0-1  
mingw-w64-ucrt-x86_64-tcl 8.6.12-2  
mingw-w64-ucrt-x86_64-termcap 1.3.1-7  
mingw-w64-ucrt-x86_64-tk 8.6.12-2  
mingw-w64-ucrt-x86_64-tzdata 2023c-1  
mingw-w64-ucrt-x86_64-windows-default-manifest 6.4-4  
mingw-w64-ucrt-x86_64-winpthreads-git 11.0.0.r404.g3a137bd87-1  
mingw-w64-ucrt-x86_64-xxhash 0.8.2-1  
mingw-w64-ucrt-x86_64-xz 5.4.5-1  
mingw-w64-ucrt-x86_64-zlib 1.3-1  
mingw-w64-ucrt-x86_64-zstd 1.5.5-1  
mingw-w64-x86_64-adwaita-icon-theme 44.0-1  
mingw-w64-x86_64-aom 3.7.1-1  
mingw-w64-x86_64-atk 2.50.0-1  
mingw-w64-x86_64-binutils 2.41-2  
mingw-w64-x86_64-brotli 1.1.0-1  
mingw-w64-x86_64-bzip2 1.0.8-3  
mingw-w64-x86_64-c-ares 1.22.1-1  
mingw-w64-x86_64-ca-certificates 20230311-1  
mingw-w64-x86_64-cairo 1.18.0-1  
mingw-w64-x86_64-clang 17.0.4-1  
mingw-w64-x86_64-clang-analyzer 17.0.4-1  
mingw-w64-x86_64-clang-tools-extra 17.0.4-1  
mingw-w64-x86_64-crt-git 11.0.0.r404.g3a137bd87-1  
mingw-w64-x86_64-curl 8.4.0-2  
mingw-w64-x86_64-dav1d 1.3.0-2  
mingw-w64-x86_64-dbus 1.14.10-1  
mingw-w64-x86_64-devil 1.8.0-11  
mingw-w64-x86_64-expat 2.5.0-1  
mingw-w64-x86_64-fontconfig 2.14.2-1  
mingw-w64-x86_64-freeglut 3.4.0-2  
mingw-w64-x86_64-freetype 2.13.2-1  
mingw-w64-x86_64-fribidi 1.0.13-1  
mingw-w64-x86_64-gcc 13.2.0-2  
mingw-w64-x86_64-gcc-libs 13.2.0-2  
mingw-w64-x86_64-gdb 13.2-3  
mingw-w64-x86_64-gdk-pixbuf2 2.42.10-4  
mingw-w64-x86_64-gettext 0.22.4-3  
mingw-w64-x86_64-ghostscript 10.02.1-1  
mingw-w64-x86_64-giflib 5.2.1-3  
mingw-w64-x86_64-glib2 2.78.1-1  
mingw-w64-x86_64-gmp 6.3.0-2  
mingw-w64-x86_64-gnuplot 5.4.9-1  
mingw-w64-x86_64-gnutls 3.8.2-1  
mingw-w64-x86_64-graphite2 1.3.14-3  
mingw-w64-x86_64-graphviz 9.0.0-1  
mingw-w64-x86_64-gtk-update-icon-cache 3.24.38.r43.g0f717ca-1  
mingw-w64-x86_64-gtk2 2.24.33-6  
mingw-w64-x86_64-gtkwave 3.3.117-1  
mingw-w64-x86_64-gts 0.7.6-2  
mingw-w64-x86_64-harfbuzz 8.3.0-1  
mingw-w64-x86_64-headers-git 11.0.0.r404.g3a137bd87-1  
mingw-w64-x86_64-hicolor-icon-theme 0.17-3  
mingw-w64-x86_64-icu 74.1-1  
mingw-w64-x86_64-isl 0.26-1  
mingw-w64-x86_64-jasper 4.0.0-1  
mingw-w64-x86_64-jbig2dec 0.20-1  
mingw-w64-x86_64-jbigkit 2.1-5  
mingw-w64-x86_64-lcms2 2.15-4  
mingw-w64-x86_64-lerc 4.0.0-1  
mingw-w64-x86_64-libavif 1.0.2-1  
mingw-w64-x86_64-libc++ 17.0.4-1  
mingw-w64-x86_64-libcaca 0.99.beta20-5  
mingw-w64-x86_64-libcerf 1~1.17-1  
mingw-w64-x86_64-libdatrie 0.2.13-3  
mingw-w64-x86_64-libde265 1.0.14-1  
mingw-w64-x86_64-libdeflate 1.19-1  
mingw-w64-x86_64-libffi 3.4.4-1  
mingw-w64-x86_64-libgcrypt 1.10.3-1  
mingw-w64-x86_64-libgd 2.3.2-8  
mingw-w64-x86_64-libgpg-error 1.47-2  
mingw-w64-x86_64-libheif 1.17.5-1  
mingw-w64-x86_64-libiconv 1.17-3  
mingw-w64-x86_64-libidn 1.41-1  
mingw-w64-x86_64-libidn2 2.3.4-1  
mingw-w64-x86_64-libimagequant 4.2.2-1  
mingw-w64-x86_64-libjpeg-turbo 3.0.1-1  
mingw-w64-x86_64-libltdl 2.4.7-2  
mingw-w64-x86_64-libmng 2.0.3-6  
mingw-w64-x86_64-libpaper 2.1.0-2  
mingw-w64-x86_64-libpng 1.6.40-1  
mingw-w64-x86_64-libpsl 0.21.2-4  
mingw-w64-x86_64-librsvg 2.57.0-2  
mingw-w64-x86_64-libsecret 0.20.5-2  
mingw-w64-x86_64-libsquish 1.15-4  
mingw-w64-x86_64-libssh2 1.11.0-2  
mingw-w64-x86_64-libsystre 1.0.1-4  
mingw-w64-x86_64-libtasn1 4.19.0-1  
mingw-w64-x86_64-libthai 0.1.29-3  
mingw-w64-x86_64-libtiff 4.6.0-1  
mingw-w64-x86_64-libtre-git r128.6fb7206-2  
mingw-w64-x86_64-libunistring 1.1-1  
mingw-w64-x86_64-libwebp 1.3.2-1  
mingw-w64-x86_64-libwinpthread-git 11.0.0.r404.g3a137bd87-1  
mingw-w64-x86_64-libxml2 2.12.1-1  
mingw-w64-x86_64-libyuv 1864.r2426.464c51a0-1  
mingw-w64-x86_64-lld 17.0.4-1  
mingw-w64-x86_64-lldb 17.0.4-1  
mingw-w64-x86_64-llvm 17.0.4-1  
mingw-w64-x86_64-llvm-libs 17.0.4-1  
mingw-w64-x86_64-lua 5.4.6-2  
mingw-w64-x86_64-lzo2 2.10-2  
mingw-w64-x86_64-make 4.4-2  
mingw-w64-x86_64-mpc 1.3.1-2  
mingw-w64-x86_64-mpdecimal 2.5.1-1  
mingw-w64-x86_64-mpfr 4.2.1-2  
mingw-w64-x86_64-ncurses 6.4.20230708-1  
mingw-w64-x86_64-nettle 3.9.1-1  
mingw-w64-x86_64-nghttp2 1.58.0-1  
mingw-w64-x86_64-nspr 4.35-2  
mingw-w64-x86_64-nss 3.93-1  
mingw-w64-x86_64-openjpeg2 2.5.0-4  
mingw-w64-x86_64-openssl 3.2.0-1  
mingw-w64-x86_64-p11-kit 0.25.3-1  
mingw-w64-x86_64-pango 1.50.14-4  
mingw-w64-x86_64-pcre2 10.42-1  
mingw-w64-x86_64-pixman 0.42.2-1  
mingw-w64-x86_64-poppler 23.08.0-2  
mingw-w64-x86_64-poppler-data 0.4.12-1  
mingw-w64-x86_64-python 3.11.6-2  
mingw-w64-x86_64-python-cachetools 5.3.2-1  
mingw-w64-x86_64-python-cffi 1.16.0-1  
mingw-w64-x86_64-python-cryptography 41.0.5-1  
mingw-w64-x86_64-python-distlib 0.3.7-1  
mingw-w64-x86_64-python-future 0.18.3-2  
mingw-w64-x86_64-python-pefile 2023.2.7-2  
mingw-w64-x86_64-python-pip 23.3.1-1  
mingw-w64-x86_64-python-ply 3.11-4  
mingw-w64-x86_64-python-py2exe 0.13.0.0-2  
mingw-w64-x86_64-python-pycparser 2.21-3  
mingw-w64-x86_64-python-pyopenssl 23.3.0-1  
mingw-w64-x86_64-python-setuptools 68.2.2-1  
mingw-w64-x86_64-python-six 1.16.0-4  
mingw-w64-x86_64-rav1e 0.6.6-2  
mingw-w64-x86_64-readline 8.2.001-6  
mingw-w64-x86_64-sed 4.9-1  
mingw-w64-x86_64-shared-mime-info 2.4-1  
mingw-w64-x86_64-sqlite3 3.44.0-1  
mingw-w64-x86_64-svt-av1 1.7.0-1  
mingw-w64-x86_64-tcl 8.6.12-2  
mingw-w64-x86_64-tcllib 1.21-2  
mingw-w64-x86_64-termcap 1.3.1-7  
mingw-w64-x86_64-tk 8.6.12-2  
mingw-w64-x86_64-tklib 0.7.0+2.72e4dbf28c-1  
mingw-w64-x86_64-tzdata 2023c-1  
mingw-w64-x86_64-wget 1.21.4-1  
mingw-w64-x86_64-windows-default-manifest 6.4-4  
mingw-w64-x86_64-wineditline 2.206-1  
mingw-w64-x86_64-winpthreads-git 11.0.0.r404.g3a137bd87-1  
mingw-w64-x86_64-wxwidgets3.2-common-libs 3.2.4-1  
mingw-w64-x86_64-wxwidgets3.2-msw-libs 3.2.4-1  
mingw-w64-x86_64-x265 3.5-3  
mingw-w64-x86_64-xpm-nox 4.2.0-8  
mingw-w64-x86_64-xxhash 0.8.2-1  
mingw-w64-x86_64-xz 5.4.5-1  
mingw-w64-x86_64-zlib 1.3-1  
mingw-w64-x86_64-zstd 1.5.5-1  
mintty 1~3.7.0-1  
mpc 1.3.1-1  
mpdecimal 2.5.1-2  
mpfr 4.2.1-1  
msys2-keyring 1~20231013-1  
msys2-launcher 1.5-1  
msys2-runtime 3.4.9-3  
msys2-runtime-devel 3.4.9-3  
msys2-w32api-headers 11.0.1.r0.gc3e587c06-2  
msys2-w32api-runtime 11.0.1.r0.gc3e587c06-2  
nano 7.2-1  
ncurses 6.4-2  
nettle 3.9.1-1  
openssl 3.1.4-1  
p11-kit 0.25.3-1  
pacman 6.0.2-11  
pacman-contrib 1.9.1-1  
pacman-mirrors 20221016-1  
patch 2.7.6-2  
patchutils 0.4.2-3  
pcre2 10.42-1  
perl 5.36.1-2  
perl-Locale-Gettext 1.07-10  
perl-Module-Build 0.4231-1  
perl-Test-Pod 1.52-2  
perl-XML-Parser 2.46-6  
perl-YAML-Syck 1.34-2  
perl-inc-latest 0.500-1  
pinentry 1.2.1-1  
pkgconf 2.1.0-1  
python 3.11.6-1  
python-pip 23.3.1-1  
python-setuptools 68.2.0-1  
quilt 0.67-1  
rebase 4.5.0-4  
reflex 20230523-1  
scons 3.1.2-9  
sed 4.9-1  
swig 4.1.1-2  
tar 1.35-2  
tcl 8.6.12-3  
texinfo 7.1-1  
texinfo-tex 7.1-1  
tftp-hpa 5.2-4  
time 1.9-3  
tzcode 2023c-1  
unrar 6.2.12-1  
util-linux 2.35.2-4  
wget 1.21.4-1  
which 2.21-4  
windows-default-manifest 6.4-1  
xmlto 0.0.28-4  
xxhash 0.8.2-1  
xz 5.4.5-1  
zip 3.0-3  
zlib 1.3-1  
zstd 1.5.5-1  
```  
  
The log from conan using b2:  
```  
PS F:\Files\Git\Msys2LibUrlRepro> conan_install_mingw.ps1       
Building a 'Release' build  
Host profile: 'mingw'  
Build profile: 'mingw'  
Type: 'install'  
Running: 'conan install --profile:build=mingw --profile:host=mingw --build=missing -s build_type=Release --output-folder build .'  
  
======== Input profiles ========  
Profile host:  
[settings]  
arch=x86_64  
build_type=Release  
compiler=gcc  
compiler.exception=seh  
compiler.libcxx=libstdc++  
compiler.threads=posix  
compiler.version=13  
os=Windows  
os.subsystem=msys2  
[options]  
boost/*:without_fiber=True  
boost/*:without_python=True  
boost/*:without_stacktrace=True  
[conf]  
tools.cmake.cmaketoolchain:generator=Ninja  
tools.build:compiler_executables={'asm': 'C:/utils/msys64/mingw64/bin/as.exe', 'cpp': 'C:/utils/msys64/mingw64/bin/g++.exe', 'c': 'C:/utils/msys64/mingw64/bin/gcc.exe'}  
[buildenv]  
PATH+=(path)C:/utils/msys64/mingw64/bin  
  
Profile build:  
[settings]  
arch=x86_64  
build_type=Release  
compiler=gcc  
compiler.exception=seh  
compiler.libcxx=libstdc++  
compiler.threads=posix  
compiler.version=13  
os=Windows  
os.subsystem=msys2  
[options]  
boost/*:without_fiber=True  
boost/*:without_python=True  
boost/*:without_stacktrace=True  
[conf]  
tools.cmake.cmaketoolchain:generator=Ninja  
tools.build:compiler_executables={'asm': 'C:/utils/msys64/mingw64/bin/as.exe', 'cpp': 'C:/utils/msys64/mingw64/bin/g++.exe', 'c': 'C:/utils/msys64/mingw64/bin/gcc.exe'}  
[buildenv]  
PATH+=(path)C:/utils/msys64/mingw64/bin  
  
  
======== Computing dependency graph ========  
Graph root  
    conanfile.txt: F:\Files\Git\Msys2LibUrlRepro\conanfile.txt  
Requirements  
    boost/1.83.0#7825569cd0622461dec7bc87dfdf47ae - Cache  
    bzip2/1.0.8#411fc05e80d47a89045edc1ee6f23c1d - Cache  
    zlib/1.2.13#3481ac22c74e686ac031d5e8b774bc9d - Cache  
Build requirements  
    b2/4.10.1#8dc3df1cc73ad65d86cbdfd31fdb011f - Cache  
Resolved version ranges  
    zlib/[>=1.2.11 <2]: zlib/1.2.13  
  
======== Computing necessary packages ========  
Requirements  
    boost/1.83.0#7825569cd0622461dec7bc87dfdf47ae:cd97b83499419a19b0479aad7f54c0cad8fb5366 - Build  
    bzip2/1.0.8#411fc05e80d47a89045edc1ee6f23c1d:6e9b9b1a86140d88530f8ae0c68f4347e4bff308#a91cf6cc6e6554e979b54a62e533e044 - Cache  
    zlib/1.2.13#3481ac22c74e686ac031d5e8b774bc9d:47e3428045e3ad1fb896666c8b9924ea937a9d58#61ba1049f74dac2e7a5e2da0c8c78926 - Cache  
Build requirements  
    b2/4.10.1#8dc3df1cc73ad65d86cbdfd31fdb011f:cb252b2c440d438481122cc116aa369ad454f1a3#6aa84338661fb2eddb9264246d8d0a7f - Cache  
  
======== Installing packages ========  
b2/4.10.1: Already installed! (1 of 4)  
bzip2/1.0.8: Already installed! (2 of 4)  
zlib/1.2.13: Already installed! (3 of 4)  
  
-------- Installing package boost/1.83.0 (4 of 4) --------  
boost/1.83.0: Building from source  
boost/1.83.0: Package boost/1.83.0:cd97b83499419a19b0479aad7f54c0cad8fb5366  
boost/1.83.0: Building your package in C:\Users\David\.conan2\p\b\boostad237e41cdbb5\b  
boost/1.83.0: Calling generate()  
boost/1.83.0: Generators folder: C:\Users\David\.conan2\p\b\boostad237e41cdbb5\b\build-release\conan  
boost/1.83.0: Generating aggregated env files  
boost/1.83.0: Generated aggregated env files: ['conanbuild.bat', 'conanrun.bat']  
boost/1.83.0: Calling build()  
boost/1.83.0: WARN: replace_in_file didn't find pattern '/* thread_local */' in 'C:\Users\David\.conan2\p\boost1d9916eed0e5d\s\src\boost\stacktrace\detail\libbacktrace_impls.hpp' file.  
boost/1.83.0: WARN: replace_in_file didn't find pattern '/* static __thread */' in 'C:\Users\David\.conan2\p\boost1d9916eed0e5d\s\src\boost\stacktrace\detail\libbacktrace_impls.hpp' file.  
boost/1.83.0: WARN: replace_in_file didn't find pattern 'local generic-os = [ set.difference $(all-os) : aix darwin vxworks solaris osf hpux ] ;' in 'C:\Users\David\.conan2\p\boost1d9916eed0e5d\s\src\tools\build\src\tools\gcc.jam' file.  
boost/1.83.0: WARN: replace_in_file didn't find pattern 'local no-threading = android beos haiku sgi darwin vxworks ;' in 'C:\Users\David\.conan2\p\boost1d9916eed0e5d\s\src\tools\build\src\tools\gcc.jam' file.  
boost/1.83.0: WARN: replace_in_file didn't find pattern '    <conditional>@numa' in 'C:\Users\David\.conan2\p\boost1d9916eed0e5d\s\src\libs\fiber\build\Jamfile.v2' file.  
boost/1.83.0: WARN: Patching user-config.jam  
boost/1.83.0: WARN:  
using zlib : 1.2.13 : <include>"C:/Users/David/.conan2/p/b/zlibd60a3a7a52e8c/p/include" <search>"C:/Users/David/.conan2/p/b/zlibd60a3a7a52e8c/p/lib" <name>z ;  
using bzip2 : 1.0.8 : <include>"C:/Users/David/.conan2/p/b/bzip21c8e46e265834/p/include" <search>"C:/Users/David/.conan2/p/b/bzip21c8e46e265834/p/lib" <name>bz2 ;  
using "gcc" :  :  "C:/utils/msys64/mingw64/bin/g++.exe" :  
 ;  
boost/1.83.0: WARN: b2 -q target-os=windows architecture=x86 address-model=64 binary-format=pe abi=ms --layout=system --user-config=C:\Users\David\.conan2\p\boost1d9916eed0e5d\s\src\tools\build\user-config.jam -sNO_ZLIB=0 -sNO_BZIP2=0 -sNO_LZMA=1 -sNO_ZSTD=1 boost.locale.icu=off --disable-icu boost.locale.iconv=off --disable-iconv threading=multi visibility=hidden link=static variant=release --with-atomic --with-chrono --with-container --with-context --with-contract --with-coroutine --with-date_time --with-exception --with-filesystem --with-graph --with-iostreams --with-json --with-locale --with-log --with-math --with-nowide --with-program_options --with-random --with-regex --with-serialization --with-system --with-test --with-thread --with-timer --with-type_erasure --with-url --with-wave toolset=gcc define=_GLIBCXX_USE_CXX11_ABI=0 pch=on linkflags="" cxxflags="-fPIC" install --prefix=C:\Users\David\.conan2\p\b\boostad237e41cdbb5\p -j32 --abbreviate-paths -d0 --debug-configuration --build-dir="C:\Users\David\.conan2\p\b\boostad237e41cdbb5\b\build-release"  
boost/1.83.0: RUN: b2 -q target-os=windows architecture=x86 address-model=64 binary-format=pe abi=ms --layout=system --user-config=C:\Users\David\.conan2\p\boost1d9916eed0e5d\s\src\tools\build\user-config.jam -sNO_ZLIB=0 -sNO_BZIP2=0 -sNO_LZMA=1 -sNO_ZSTD=1 boost.locale.icu=off --disable-icu boost.locale.iconv=off --disable-iconv threading=multi visibility=hidden link=static variant=release --with-atomic --with-chrono --with-container --with-context --with-contract --with-coroutine --with-date_time --with-exception --with-filesystem --with-graph --with-iostreams --with-json --with-locale --with-log --with-math --with-nowide --with-program_options --with-random --with-regex --with-serialization --with-system --with-test --with-thread --with-timer --with-type_erasure --with-url --with-wave toolset=gcc define=_GLIBCXX_USE_CXX11_ABI=0 pch=on linkflags="" cxxflags="-fPIC" install --prefix=C:\Users\David\.conan2\p\b\boostad237e41cdbb5\p -j32 --abbreviate-paths -d0 --debug-configuration --build-dir="C:\Users\David\.conan2\p\b\boostad237e41cdbb5\b\build-release"  
notice: found boost-build.jam at C:/Users/David/.conan2/p/boost1d9916eed0e5d/s/src/boost-build.jam  
notice: loading B2 from C:/Users/David/.conan2/p/b/b238f1656eade55/p/bin/.b2/kernel/bootstrap.jam  
notice: Searching 'C:\Users\David' 'C:\Users\David' 'C:/Users/David/.conan2/p/b/b238f1656eade55/p/bin/.b2/kernel' 'C:/Users/David/.conan2/p/b/b238f1656eade55/p/bin/.b2/util' 'C:/Users/David/.conan2/p/b/b238f1656eade55/p/bin/.b2/build' 'C:/Users/David/.conan2/p/b/b238f1656eade55/p/bin/.b2/tools' 'C:/Users/David/.conan2/p/b/b238f1656eade55/p/bin/.b2/contrib' 'C:/Users/David/.conan2/p/b/b238f1656eade55/p/bin/.b2/.' for site-config configuration file 'site-config.jam'.  
notice: Configuration file 'site-config.jam' not found in 'C:\Users\David' 'C:\Users\David' 'C:/Users/David/.conan2/p/b/b238f1656eade55/p/bin/.b2/kernel' 'C:/Users/David/.conan2/p/b/b238f1656eade55/p/bin/.b2/util' 'C:/Users/David/.conan2/p/b/b238f1656eade55/p/bin/.b2/build' 'C:/Users/David/.conan2/p/b/b238f1656eade55/p/bin/.b2/tools' 'C:/Users/David/.conan2/p/b/b238f1656eade55/p/bin/.b2/contrib' 'C:/Users/David/.conan2/p/b/b238f1656eade55/p/bin/.b2/.'.  
notice: Loading explicitly specified user configuration file:  
    C:\Users\David\.conan2\p\boost1d9916eed0e5d\s\src\tools\build\user-config.jam  
notice: Searching 'C:\Users\David\.conan2\p\boost1d9916eed0e5d\s\src\tools\build' for user-config configuration file 'user-config.jam'.  
notice: Loading user-config configuration file 'user-config.jam' from 'C:/Users/David/.conan2/p/boost1d9916eed0e5d/s/src/tools/build'.  
notice: [zlib] Using pre-installed library  
notice: [zlib] Condition  
notice: [bzip2] Using pre-installed library  
notice: [bzip2] Condition  
notice: will use 'C:/utils/msys64/mingw64/bin/g++.exe' for gcc, condition <toolset>gcc-13  
notice: using gcc libraries :: <toolset>gcc-13 :: C:/utils/msys64/mingw64/bin C:/utils/msys64/mingw64/lib C:/utils/msys64/mingw64/lib32 C:/utils/msys64/mingw64/lib64  
notice: using gcc archiver :: <toolset>gcc-13 :: C:/utils/msys64/mingw64/x86_64-w64-mingw32/bin/ar.exe  
notice: using rc compiler :: <toolset>gcc-13 :: C:\utils\msys64\mingw64\bin\windres.exe  
notice: [zlib] zlib is already configured  
notice: [bzip2] bzip is already configured  
notice: iostreams: not using lzma compression  
notice: iostreams: not using zstd compression  
notice: [python-cfg] Configuring python...  
notice: [python-cfg] Checking interpreter command "python"...  
notice: [python-cfg] running command 'DIR /-C /A:S "C:\utils\msys64\mingw64\bin\python.exe" 2>&1'  
notice: [python-cfg] running command 'python -c "from sys import *; print('version=%d.%d\nplatform=%s\nprefix=%s\nexec_prefix=%s\nexecutable=%s' % (version_info[0],version_info[1],platform,prefix,exec_prefix,executable))" 2>&1'  
notice: [python-cfg] ...requested configuration matched!  
notice: [python-cfg] Details of this Python configuration:  
notice: [python-cfg]   interpreter command: "python"  
notice: [python-cfg]   include path: "C:\utils\msys64\mingw64\Include"  
notice: [python-cfg]   library path: "C:\utils\msys64\mingw64\libs"  
notice: [python-cfg]   DLL search path: "C:\utils\msys64\mingw64"  
notice: [python-cfg] Checking for NumPy...  
notice: [python-cfg] running command 'python -c "import sys; sys.stderr = sys.stdout; import numpy; print(numpy.get_include())"'  
notice: [python-cfg] NumPy disabled. Reason:  
notice: [python-cfg]   python -c "import sys; sys.stderr = sys.stdout; import numpy; print(numpy.get_include())" aborted with  
notice: [python-cfg]   Traceback (most recent call last):  
  File "<string>", line 1, in <module>  
ModuleNotFoundError: No module named 'numpy'  
Performing configuration checks  
  
    - default address-model    : 64-bit [1]  
    - default architecture     : x86 [1]  
    - compiler supports SSE2   : yes [2]  
    - compiler supports SSE4.1 : yes [2]  
    - has synchronization.lib  : yes [2]  
    - has std::atomic_ref      : no [2]  
    - has statx                : no [2]  
    - has statx syscall        : no [2]  
    - has BCrypt API           : yes [2]  
    - has init_priority attribute : yes [2]  
    - has stat::st_blksize     : no [2]  
    - has stat::st_mtim        : no [2]  
    - has stat::st_mtimensec   : no [2]  
    - has stat::st_mtimespec   : no [2]  
    - has stat::st_birthtim    : no [2]  
    - has stat::st_birthtimensec : no [2]  
    - has stat::st_birthtimespec : no [2]  
    - has fdopendir(O_NOFOLLOW) : no [2]  
    - has dirent::d_type       : no [2]  
    - has POSIX *at APIs       : no [2]  
    - has_icu builds           : no [2]  
    - zlib                     : yes [3]  
    - bzip2                    : yes [3]  
    - cxx11_constexpr          : yes [2]  
    - cxx11_decltype           : yes [2]  
    - cxx11_hdr_tuple          : yes [2]  
    - cxx11_template_aliases   : yes [2]  
    - cxx11_variadic_templates : yes [2]  
    - cxx11_basic_alignas      : yes [2]  
    - cxx11_auto_declarations  : yes [2]  
    - cxx11_defaulted_functions : yes [2]  
    - cxx11_defaulted_moves    : yes [2]  
    - cxx11_hdr_functional     : yes [2]  
    - cxx11_hdr_type_traits    : yes [2]  
    - cxx11_noexcept           : yes [2]  
    - cxx11_nullptr            : yes [2]  
    - cxx11_override           : yes [2]  
    - cxx11_range_based_for    : yes [2]  
    - cxx11_rvalue_references  : yes [2]  
    - cxx11_scoped_enums       : yes [2]  
    - cxx11_smart_ptr          : yes [2]  
    - cxx11_static_assert      : yes [2]  
    - native atomic int32 supported : yes [2]  
    - has message compiler     : no [2]  
    - native syslog supported  : no [2]  
    - pthread supports robust mutexes : no [2]  
    - compiler supports SSSE3  : yes [2]  
    - compiler supports AVX2   : yes [2]  
    - gcc visibility           : yes [2]  
    - sfinae_expr              : yes [2]  
    - cxx11_lambdas            : yes [2]  
    - cxx11_unified_initialization_syntax : yes [2]  
    - cxx11_hdr_initializer_list : yes [2]  
    - cxx11_hdr_chrono         : yes [2]  
    - cxx11_thread_local       : yes [2]  
    - cxx11_numeric_limits     : yes [2]  
    - cxx11_hdr_array          : yes [2]  
    - cxx11_hdr_atomic         : yes [2]  
    - cxx11_allocator          : yes [2]  
    - cxx11_explicit_conversion_operators : yes [2]  
    - gcc visibility           : yes [4]  
    - cxx11_noexcept           : yes [4]  
    - cxx11_rvalue_references  : yes [4]  
    - sfinae_expr              : yes [4]  
    - cxx11_auto_declarations  : yes [4]  
    - cxx11_lambdas            : yes [4]  
    - cxx11_unified_initialization_syntax : yes [4]  
    - cxx11_hdr_tuple          : yes [4]  
    - cxx11_hdr_initializer_list : yes [4]  
    - cxx11_hdr_chrono         : yes [4]  
    - cxx11_thread_local       : yes [4]  
    - cxx11_constexpr          : yes [4]  
    - cxx11_nullptr            : yes [4]  
    - cxx11_numeric_limits     : yes [4]  
    - cxx11_decltype           : yes [4]  
    - cxx11_hdr_array          : yes [4]  
    - cxx11_hdr_atomic         : yes [4]  
    - cxx11_hdr_type_traits    : yes [4]  
    - cxx11_allocator          : yes [4]  
    - cxx11_explicit_conversion_operators : yes [4]  
    - long double support      : yes [4]  
    - cxx11_char16_t           : yes [2]  
    - cxx11_char32_t           : yes [2]  
    - Has Large File Support   : yes [2]  
    - Has attribute init_priority : yes [2]  
    - BOOST_COMP_GNUC >= 4.3.0 : yes [2]  
    - cxx11_hdr_thread         : yes [2]  
    - cxx11_hdr_mutex          : yes [2]  
    - cxx11_hdr_regex          : yes [2]  
  
[1] gcc-13  
[2] gcc-13/rls/bst.l-off/bst.l-off/lnk-sttc/pythn-3.1/thrdp-wn32/thrd-mlt/vsblt-hdn  
[3] lnk-sttc  
[4] gcc-13/rls/bst.l-off/bst.l-off/lnk-sttc/pch-off/pythn-3.1/thrdp-wn32/thrd-mlt/vsblt-hdn  
  
Component configuration:  
  
    - atomic                   : building  
    - chrono                   : building  
    - container                : building  
    - context                  : building  
    - contract                 : building  
    - coroutine                : building  
    - date_time                : building  
    - exception                : building  
    - fiber                    : not building  
    - filesystem               : building  
    - graph                    : building  
    - graph_parallel           : not building  
    - headers                  : not building  
    - iostreams                : building  
    - json                     : building  
    - locale                   : building  
    - log                      : building  
    - math                     : building  
    - mpi                      : not building  
    - nowide                   : building  
    - program_options          : building  
    - python                   : not building  
    - random                   : building  
    - regex                    : building  
    - serialization            : building  
    - stacktrace               : not building  
    - system                   : building  
    - test                     : building  
    - thread                   : building  
    - timer                    : building  
    - type_erasure             : building  
    - url                      : building  
    - wave                     : building  
  
...removing C:\Users\David\.conan2\p\b\boostad237e41cdbb5\b\build-release\boost\bin.v2\libs\url\build\gcc-13\rls\lnk-sttc\thrdp-wn32\thrd-mlt\vsblt-hdn\libboost_url.a  
...failed updating 1 target...  
  
boost/1.83.0: ERROR:   
Package 'cd97b83499419a19b0479aad7f54c0cad8fb5366' build failed  
boost/1.83.0: WARN: Build folder C:\Users\David\.conan2\p\b\boostad237e41cdbb5\b\build-release  
*********************************************************  
Recipe 'boost/1.83.0' cannot build its binary  
It is possible that this recipe is not Conan 2.0 ready  
If the recipe comes from ConanCenter check: https://conan.io/cci-v2.html  
If it is your recipe, check if it is updated to 2.0  
*********************************************************  
  
ERROR: boost/1.83.0: Error in build() method, line 887  
        self.run(full_command)  
        ConanException: Error 1 while executing  
```

---

## Comment 15

> Username: pdimov  
> Created at: 2023-12-17 02:44:06 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1859020010  

I have a simpler way to reproduce: [install MSYS2 from the official page](https://www.msys2.org/), install `mingw-w64-ucrt-x86_64-gcc` as suggested there, download Boost 1.83.0, unpack, `./bootstrap.sh`, `./b2 toolset=gcc --with-url`, fails.  
  
Interestingly, this seems to be specific to URL. The other libraries I tried build fine.

---

## Comment 16

> Username: pdimov  
> Created at: 2023-12-17 02:52:32 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1859021081  

I confirm that the generated response file has paths with backslashes in it, which aren't handled correctly by `ar`, and I see nothing wrong with URL's Jamfile. This looks like a B2 issue. @grafikrobot? @grisumbras? @Kojoley?

---

## Comment 17

> Username: pdimov  
> Created at: 2023-12-18 18:05:12 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1861217071  

The problem is with this line:  
  
https://github.com/bfgroup/b2/blob/5dca2965cb083060137498d6581fcbfb9522cfbc/src/tools/gcc.jam#L1094  
  
The final `:E="$(>)"` needs to be changed to `:E="$(>:T)"`.  
  
This fixes the static version. I don't get an error while building the dynamic version, and the `:T` seems properly present there already, so I'm not sure why the above doesn't work.

---

## Comment 18

> Username: pdimov  
> Created at: 2023-12-18 18:10:56 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1861230988  

Reported upstream as https://github.com/bfgroup/b2/issues/352.

---

## Comment 19

> Username: babelvit  
> Created at: 2023-12-19 11:19:18 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1862575449  

@pdimov All MinGW users appreciate your effort. Thank you.

---

## Comment 20

> Username: grafikrobot  
> Created at: 2023-12-21 04:55:43 UTC  
> Url: https://github.com/boostorg/url/issues/779#issuecomment-1865480278  

Fixed with https://github.com/bfgroup/b2/commit/2474be56b42e191bbc0706d46177703b24a7e7c3
