# #320 - QA issue with dev-util/boost-build-1.65.0 Gentoo package [Closed]

> Username: ghost  
> Created at: 2018-06-23 16:15:51 UTC  
> Updated at: 2024-10-31 01:11:51 UTC  
> Closed at: 2024-10-31 01:11:50 UTC  
> Url: https://github.com/boostorg/build/issues/320  

My GNU/Linux distribution is Gentoo Linux.  
  
The Portage package manager reports the following warning message when building `dev-util/boost-build-1.65.0`:  
  
```  
 * QA Notice: Package triggers severe warnings which indicate that it  
 *            may exhibit random runtime failures.  
 * execcmd.c:120:5: warning: implicit declaration of function ‘out_printf’; did you mean ‘lol_print’? [-Wimplicit-function-declaration]  
 * jam.c:669:5: warning: implicit declaration of function ‘exec_done’; did you mean ‘regex_done’? [-Wimplicit-function-declaration]  
 * make.c:105:5: warning: implicit declaration of function ‘exec_init’; did you mean ‘lol_init’? [-Wimplicit-function-declaration]  
 * make.c:136:13: warning: implicit declaration of function ‘out_printf’; did you mean ‘lol_print’? [-Wimplicit-function-declaration]  
 * filesys.c:360:10: warning: implicit declaration of function ‘filelist_empty’; did you mean ‘filelist_next’? [-Wimplicit-function-declaration]  
 * modules/path.c:16:12: warning: implicit declaration of function ‘file_query’ [-Wimplicit-function-declaration]  
 * fileunix.c:236:10: warning: implicit declaration of function ‘filelist_empty’; did you mean ‘filelist_next’? [-Wimplicit-function-declaration]  
 * fileunix.c:238:14: warning: implicit declaration of function ‘file_collect_archive_content_’; did you mean ‘file_collect_dir_content_’? [-Wimplicit-function-declaration]  
  
 * Please do not file a Gentoo bug and instead report the above QA  
 * issues directly to the upstream developers of this software.  
 * Homepage: http://www.boost.org/doc/tools/build/index.html  
```  
  
I also attached the full build log.  
  
[build.log](https://github.com/boostorg/build/files/2130050/build.log)

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-06-29 15:35:33 UTC  
> Url: https://github.com/boostorg/build/issues/320#issuecomment-401391464  

AMDG  
  
  I'm not sure why this would happen.  The headers  
containing these declarations are #included and  
I've never seen these warnings from my own  
builds.  The only thing I can think of is that  
the header might have been hijacked somehow and  
actually #includes a totally unrelated file instead.  
(output.h, execcmd.h, filesys.h)  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:26 UTC  
> Url: https://github.com/boostorg/build/issues/320#issuecomment-868936445  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 3

> Username: t3sting3  
> Created at: 2024-10-30 23:30:46 UTC  
> Url: https://github.com/boostorg/build/issues/320#issuecomment-2448667393  

It is a breaking error using:  
Apple clang version 13.0.0 (clang-1300.0.29.3)  
Target: x86_64-apple-darwin21.6.0  
Thread model: posix  
InstalledDir: /Applications/Xcode_13.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
  
trying to compile boost 1.65.1 (i need to build something that requires this specific lib, and it breaks:  
```  
fileunix.c:238:14: error: implicit declaration of function 'file_collect_archive_content_' is invalid in C99 [-Werror,-Wimplicit-function-declaration]  
        if ( file_collect_archive_content_( archive ) < 0 )  
             ^  
fileunix.c:238:14: note: did you mean 'file_collect_dir_content_'?  
fileunix.c:110:5: note: 'file_collect_dir_content_' declared here  
int file_collect_dir_content_( file_info_t * const d )  
    ^  
1 error generated.  
```

---

## Comment 4

> Username: grafikrobot  
> Created at: 2024-10-31 01:11:50 UTC  
> Url: https://github.com/boostorg/build/issues/320#issuecomment-2448800226  

Boost 1.65.1 supported up to Apple clang 8.1.0.
