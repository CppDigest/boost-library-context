# #209 - Unable to build boost_stacktrace_addr2line with non system-default libstdc++.so [Closed]

> Username: rklepov  
> Created at: 2025-08-01 20:59:43 UTC  
> Updated at: 2025-12-11 19:16:29 UTC  
> Closed at: 2025-12-11 19:15:20 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/209  

I'm building _boost_ libraries **v1.88.0** with _clang20_ and _libstdc++_ supplied with _gcc 15.1_ both of which are installed at custom paths. Both PATH and LD_LIBRARY_PATH are configured accordingly (LD_LIBRARY_PATH is particularly important for this matter as you'll see shortly).  
  
```text  
$ clang -v  
clang version 20.1.8  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
InstalledDir: /data/local/llvm/20.1.8/bin  
Found candidate GCC installation: /data/local/gnu/gcc/15.1.0/lib/gcc/x86_64-pc-linux-gnu/15.1.0  
Selected GCC installation: /data/local/gnu/gcc/15.1.0/lib/gcc/x86_64-pc-linux-gnu/15.1.0  
Candidate multilib: .;@m64  
Selected multilib: .;@m64  
```  
  
While both `--with-stacktrace` and `-DBOOST_STACKTRACE_ADDR2LINE_LOCATION=/usr/bin/addr2line"` options are supplied and `/usr/bin/addr2line` executable is available and working, the configuration checks refuse to build `boost.stacktrace.addr2line`:  
  
```text  
    - boost.stacktrace.addr2line : no [2]  
    - boost.stacktrace.addr2line : no [4]  
    - boost.stacktrace.backtrace : yes [2]  
    - boost.stacktrace.basic   : no [2]  
    - boost.stacktrace.from_exception : yes  
    - boost.stacktrace.from_exception : yes  
```  
  
Looking through `config.log` shows that, while `addr2line_exe` executable is successfully built from [`has_addr2line.cpp`](https://github.com/boostorg/stacktrace/blob/boost-1.88.0/build/has_addr2line.cpp) source, but running it with a _modified_ LD_LIBRARY_PATH fails:  
  
```text  
LD_LIBRARY_PATH=<path to libbacktrace> [build dir]/boost/bin.v2/libs/stacktrace/build/clng-lnx-20/dbg/x86_6/cxstd-14-iso/lnk-sttc/nm-on/thrd-mlt/vsblt-hdn/addr2line_exe > [build dir]/boost/bin.v2/libs/stacktrace/build/clng-lnx-20/dbg/x86_6/cxstd-14-iso/lnk-sttc/nm-on/thrd-mlt/vsblt-hdn/addr2line.output  
  
[build dir]/boost/bin.v2/libs/stacktrace/build/clng-lnx-20/dbg/x86_6/cxstd-14-iso/lnk-sttc/nm-on/thrd-mlt/vsblt-hdn/addr2line_exe:  
/lib64/libstdc++.so.6: version `GLIBCXX_3.4.29' not found (required by [build dir]/boost/bin.v2/libs/stacktrace/build/clng-lnx-20/dbg/x86_6/cxstd-14-iso/lnk-sttc/nm-on/thrd-mlt/vsblt-hdn/addr2line_exe)  
  
...failed Jamfile<[source dir]/libs/stacktrace/build>.mp_simple_run_action [build dir]/boost/bin.v2/libs/stacktrace/build/clng-lnx-20/dbg/x86_6/cxstd-14-iso/lnk-sttc/nm-on/thrd-mlt/vsblt-hdn/addr2line.output...  
```  
  
\* I've abbreviated my local paths in the log excerpt above because I build _boost_ as a [_conan_](https://github.com/conan-io/conan-center-index/blob/master/recipes/boost/all/conanfile.py) package so the actual paths are rather long and cryptic.  
  
So the problem is that setting LD_LIBRARY_PATH environment variable to a path to _libbacktrace_ for `addr2line_exe` run overwrites other custom paths there. Therefore the version of _libstdc++.so.6_ that is picked up is not the one against which `addr2line_exe` executable was built.  
  
This is particularly important in the case of _conan_ build. The point is that it eventually fails because the build options suggest that `boost_stacktrace_addr2line` should be built but the binary is not there.  
  
```text  
ConanException: These libraries were expected to be built, but were not built: {'boost_stacktrace_addr2line'}  
```  
  
To be honest from what I see in `has_addr2line.cpp` it doesn't seem that the dependency on C++ standard library (`std::string`) is essential. So arguably the easiest way to fix the issue would be just to get rid of it.

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-12-11 19:16:29 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/209#issuecomment-3643412386  

Checked with `ldd` that the binary does not depend on libstdc++.so any more  
  
Feel free to reopen the issue, if it does not work for your system
