# #103 Check that liblzma has `lzma_cputhreads()`, to fix Mac build failure. Fixes #102. [Merged]

> Username: pdimov  
> Created at: 2019-10-26 23:42:39 UTC  
> Updated at: 2019-11-20 02:37:05 UTC  
> Merged at: 2019-11-19 05:06:59 UTC  
> Closed at: 2019-11-19 05:07:00 UTC  
> Url: https://github.com/boostorg/iostreams/pull/103  



---

## Comment 1

> Username: mclow  
> Created_at: 2019-11-18 22:14:27 UTC  
> Url: https://github.com/boostorg/iostreams/pull/103#issuecomment-555233970  

I applied this patch locally (Mac OS 10.14.6), and got the following results:  
```  
$ ./b2 iostreams  
warning: mismatched versions of Boost.Build engine and core  
warning: Boost.Build engine (./b2) is 2018.02.00  
warning: Boost.Build core (at /Sources/Boost/main/tools/build/src) is 4.0-git  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - zlib                     : yes  
    - bzip2                    : yes  
    - lzma                     : yes  
    - zstd                     : no  
    - symlinks supported       : yes (cached)  
    - has_lzma_cputhreads builds : no  
...patience...  
...found 1667 targets...  
...updating 7 targets...  
clang-darwin.compile.c++ bin.v2/libs/iostreams/build/clang-darwin-10.0/debug/threading-multi/visibility-hidden/file_descriptor.o  
clang-darwin.compile.c++ bin.v2/libs/iostreams/build/clang-darwin-10.0/debug/threading-multi/visibility-hidden/mapped_file.o  
clang-darwin.compile.c++ bin.v2/libs/iostreams/build/clang-darwin-10.0/debug/threading-multi/visibility-hidden/bzip2.o  
clang-darwin.compile.c++ bin.v2/libs/iostreams/build/clang-darwin-10.0/debug/threading-multi/visibility-hidden/gzip.o  
clang-darwin.compile.c++ bin.v2/libs/iostreams/build/clang-darwin-10.0/debug/threading-multi/visibility-hidden/lzma.o  
clang-darwin.compile.c++ bin.v2/libs/iostreams/build/clang-darwin-10.0/debug/threading-multi/visibility-hidden/zlib.o  
clang-darwin.link.dll bin.v2/libs/iostreams/build/clang-darwin-10.0/debug/threading-multi/visibility-hidden/libboost_iostreams.dylib  
...updated 7 targets...  
```  
  
LGTM.

---

## Comment 2

> Username: jbonyun  
> Created_at: 2019-11-19 06:07:50 UTC  
> Url: https://github.com/boostorg/iostreams/pull/103#issuecomment-555349045  

@mclow: you're concluding that @pdimov's PR #103 fixes it for you on Mac? And that, using `check-lzma_cputhreads` means you don't also require the version change to 5.2.4 that you mentioned in the #102 thread? Because just the fact that 5.2.4 exists is news to me, and that it deletes a feature added in 5.2.0 is bothersome. So if @pdimov's changes solve it, that would be wonderful.  
  
Do any of the Travis builds have `has_lzma_cputhreads = yes`? I couldn't see one. They're based on Xenial mostly, with a few Trusty. I believe they default to 5.1.1, so wouldn't have it automatically. Is adding a Bionic build (defaults to liblzma 5.2.2) a problem that we don't want to open up? I have no experience with Travis/etc.

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-11-19 15:14:26 UTC  
> Url: https://github.com/boostorg/iostreams/pull/103#issuecomment-555552857  

Should be possible to add a Bionic job to Travis; @jeking3?

---

## Comment 4

> Username: pdimov  
> Created_at: 2019-11-19 16:52:37 UTC  
> Url: https://github.com/boostorg/iostreams/pull/103#issuecomment-555601141  

Here's a bionic run that reports "has_lzma_cputhreads builds: yes": https://travis-ci.org/boostorg/check_build/jobs/614060203

---

## Comment 5

> Username: jeking3  
> Created_at: 2019-11-20 02:37:05 UTC  
> Url: https://github.com/boostorg/iostreams/pull/103#issuecomment-555808853  

@pdimov yes, updating the CI build to the current standards and adding a bionic job should be possible.

---
