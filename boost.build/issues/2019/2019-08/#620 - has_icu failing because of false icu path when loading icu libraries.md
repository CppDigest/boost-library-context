# #620 - has_icu failing because of false icu path when loading icu libraries [Open]

> Username: kohldampfer  
> Created at: 2019-08-01 12:01:35 UTC  
> Updated at: 2021-05-29 17:22:35 UTC  
> Url: https://github.com/boostorg/build/issues/620  

I am trying to compile boost 1.70 with ICU support. For this I have these to commands in a shell script:  
  
```  
./bootstrap.sh --with-icu="$ICU_DIR}"  \  
    --with-libraries=atomic,chrono,container,context,contract,coroutine,date_time,exception,fiber,filesystem,graph,graph_parallel,iostreams,locale,log,math,mpi,program_options,random,regex,serialization,stacktrace,system,test,thread,type_erasure,timer,wave  
  
./b2 --layout=tagged \  
                cxxstd=17 \  
                toolset=gcc \  
                variant=release \  
                threading=multi \  
                link=shared \  
                address-model=64 \  
                -sZLIB_SOURCE=$ZLIB_DIR \  
                -q -sBZIP2_SOURCE=$BZIP2_DIR \  
                -sICU_PATH="${ICU_DIR}" \  
                include="${ICU_DIR}/include" \  
                -sICU_LINK="-L${ICU_DIR}/lib64 -licuuc -licudata -licui18n"  
```  
  
The variable `ICU_DIR` is pointing to a directory, where my ICU stuff is there, i.e. in `ICU_DIR/include` are the header files and in `ICU_DIR/lib64` are the libraries, e.g. `libicudata.so.61`, `libicui18n.so.61`. So I have this directory structure:  
  
```  
ICU_DIR  
ICU_DIR/bin  
ICU_DIR/bin64  
ICU_DIR/include  
ICU_DIR/lib  
ICU_DIR/lib64  
```  
  
The directories `bin` and `lib` are for 32 bit, `bin64` and `lib64` respectively for 64 bit versions of the library.  
  
During compilation I see in the logs this:  
  
>       - has_icu builds           : no  
>       ...  
>       - icu                      : yes  
  
Then I checked the files `boost_1_70_0/bin.v2/config.log`. There I can find this error  
  
> bin.v2/libs/regex/build/gcc-8.3.1/debug/cxxstd-17-iso/threading-multi/visibility-hidden/has_icu: error while loading shared libraries: libicudata.so.61: cannot open shared object file: No such file or directory  
>     LD_LIBRARY_PATH="${ICU_DIR}/bin:/opt/rh/devtoolset-8/root/usr/bin:/opt/rh/devtoolset-8/root/usr/lib:/opt/rh/devtoolset-8/root/usr/lib32:/opt/rh/devtoolset-8/root/usr/lib64:$LD_LIBRARY_PATH  
  
Then, I thought of changing my compiling command to something like this:  
  
`LD_LIBRARY_PATH=$LD_LIBRARY_PATH:${ICU_DIR}/lib64 ./b2 --layout=tagged ...`  
  
but then I get this error message during build  
  
```  
error: Name clash for '<p/path/to/boost_1_70_0/stage/lib>libboost_atomic-mt-x64.so.1.70.0'  
error:  
error: Tried to build the target twice, with property sets having  
error: these incompatible properties:  
error:  
error:     -  none  
error:     -  <dll-path>${ICU_DIR}/bin <linkflags>-L${ICU_DIR}/lib64 <linkflags>-licudata <linkflags>-licui18n <linkflags>-licuuc  
error:  
error: Please make sure to have consistent requirements for these  
error: properties everywhere in your project, especially for install  
error: targets.  
```  
  
This `dll-path` is false here. In my case, it should look to this `lib64` directory not to the `bin` directory, but I don't know how to adjsut that when starting `b2`.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:05 UTC  
> Url: https://github.com/boostorg/build/issues/620#issuecomment-850868271  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
