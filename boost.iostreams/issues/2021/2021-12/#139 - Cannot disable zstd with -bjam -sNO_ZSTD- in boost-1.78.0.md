# #139 - Cannot disable zstd with "bjam -sNO_ZSTD" in boost/1.78.0 [Open]

> Username: gcflymoto  
> Created at: 2021-12-18 20:43:19 UTC  
> Updated at: 2022-02-01 12:27:14 UTC  
> Url: https://github.com/boostorg/iostreams/issues/139  

1. The integration of bzip2 and zlib is much better in iostreams than the integration of lzma and zstd. There is a need to build boost iostreams with sandboxed versions of all the compressors instead of having bjam find and build against the host OS versions of these  
  
2. There is no equivalent of ZLIB_SOURCE/BZIP2_SOURCE for LZMA_SOURCE/ZSTD_SOURCE ??  
  
3. How do I redirect the boost iostreams build to use specific versions of LZMA and ZSTD during boost iostreams build?  
  
4. bjam -sNO_ZSTD does not work  
./tools/build/src/engine/bjam -d+2 **-sNO_ZSTD** --debug-configuration variant=debug  toolset=gcc address-model=64 visibility=global cxxflags='--std=c++11 -fPIC -ggdb3 -O0' install --prefix=./sles11_x86_64_gcc4.7.2/deb/boost_1_78_0 --stagedir=./sles11_x86_64_gcc4.7.2/deb/boost_1_78_0-build --includedir=./sles11_x86_64_gcc4.7.2/deb/boost_1_78_0/include --libdir=./sles11_x86_64_gcc4.7.2/deb/boost_1_78_0/lib  --with-atomic --with-chrono --with-container --with-context --with-contract --with-coroutine --with-date_time --with-exception --with-fiber --with-filesystem --with-graph --with-graph_parallel --with-headers --with-iostreams --with-locale --with-log --with-math --with-mpi --with-program_options --with-random --with-regex --with-serialization --with-stacktrace --with-system --with-test --with-thread --with-timer --with-type_erasure --with-json --with-python  
....  
notice: [zstd] Using pre-installed library  
notice: [zstd] Condition  
...  
    - zstd                     : yes  
  ...  
  
which leads to the inevitable linker errors  
  
-I-:/nfs/site/itools/em64t_SLES12SP5/pkgs/gcc/4.7.2/.bin/../lib64/gcc/x86_64-suse-linux/4.7.2/../../../../x86_64-suse-linux/bin/ld: warning: libzstd.so.1, needed by   
./boost/1.78.0/libs/sles12_x86_64_gcc4.7.2/opt/libboost_iostreams.so, not found (try using -rpath or -rpath-link)  
./boost/1.78.0/libs/sles12_x86_64_gcc4.7.2/opt/libboost_iostreams.so: undefined reference to `ZSTD_freeCStream'  
./boost/1.78.0/libs/sles12_x86_64_gcc4.7.2/opt/libboost_iostreams.so: undefined reference to `lzma_stream_decoder@XZ_5.0'  
./boost/1.78.0/libs/sles12_x86_64_gcc4.7.2/opt/libboost_iostreams.so: undefined reference to `ZSTD_getErrorName'  
./boost/1.78.0/libs/sles12_x86_64_gcc4.7.2/opt/libboost_iostreams.so: undefined reference to `ZSTD_createDStream'  
./boost/1.78.0/libs/sles12_x86_64_gcc4.7.2/opt/libboost_iostreams.so: undefined reference to `ZSTD_freeDStream'  
./boost/1.78.0/libs/sles12_x86_64_gcc4.7.2/opt/libboost_iostreams.so: undefined reference to `ZSTD_compressStream'  
./boost/1.78.0/libs/sles12_x86_64_gcc4.7.2/opt/libboost_iostreams.so: undefined reference to `ZSTD_decompressStream'  
./boost/1.78.0/libs/sles12_x86_64_gcc4.7.2/opt/libboost_iostreams.so: undefined reference to `lzma_code@XZ_5.0'  
./boost/1.78.0/libs/sles12_x86_64_gcc4.7.2/opt/libboost_iostreams.so: undefined reference to `ZSTD_initCStream'  
./boost/1.78.0/libs/sles12_x86_64_gcc4.7.2/opt/libboost_iostreams.so: undefined reference to `lzma_stream_encoder_mt

---

## Comment 1

> Username: rdoeffinger  
> Created at: 2022-02-01 12:27:13 UTC  
> Url: https://github.com/boostorg/iostreams/issues/139#issuecomment-1026790423  

Supporting building these compression libraries from source is really not maintainable at all.  
I don't know the build system well enough to say if there is a better way, but you should be able to use linkflags and compileflags to make it find the libraries elsewhere than the default system paths.  
And I think the syntax to disable is -sNO_ZSTD=1 and not specifying a value will not work
