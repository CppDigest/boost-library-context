# #585 - build 1.73.0 silently exiting on Jamfile.jam syntax error after compile. [Open]

> Username: userdocs  
> Created at: 2020-04-29 19:05:23 UTC  
> Updated at: 2021-05-29 17:22:43 UTC  
> Url: https://github.com/boostorg/build/issues/585  

Please see this comment.  
  
https://github.com/arvidn/libtorrent/issues/4592#issuecomment-621392398  
  
This exiting behaviour is breaking a scripted build with no clear indication of why it exited.  
  
I think it should exit earlier and provide a clear reason why.

---

## Comment 1

> Username: mloskot  
> Created at: 2020-04-29 19:24:38 UTC  
> Updated at: 2020-04-29 19:29:22 UTC  
> Url: https://github.com/boostorg/build/issues/585#issuecomment-621413923  

> I think it should exit earlier and provide a clear reason why.  
  
The exit happens as soon as possible. I don't think it can happen earlier.  
  
Which of the two (at least) mentioned in https://github.com/arvidn/libtorrent/issues/4592#issuecomment-621392398 is unclear?   
  
IMO,  the "silently" qualification used in the subject of this issue is not entirely correct.  
  
-----  
  
For the record, this complaint is related to PR #538 and its refinement in #540

---

## Comment 2

> Username: userdocs  
> Created at: 2020-04-29 19:36:55 UTC  
> Updated at: 2020-04-29 19:37:49 UTC  
> Url: https://github.com/boostorg/build/issues/585#issuecomment-621419812  

What i am experiencing is the build continues and appears to complete but then where it should move to the next stage:  
  
```  
...updated 417 targets...  
```  
 it continues the build then exits silently and there is no output to the terminal based this single warning.   
  
```  
Jamroot.jam:1: syntax error at EOF  
```  
The actual errors i listed were forced to understand the issue but not present at the point b2 exits.  
  
This behaviour did not occur in version 1.72.0.

---

## Comment 3

> Username: mloskot  
> Created at: 2020-04-29 21:37:16 UTC  
> Url: https://github.com/boostorg/build/issues/585#issuecomment-621478345  

I suspect you are experiencing the situation similar to this:  
  
I modified Boost `libs/array/test` to look as multi-project set-up, this way:  
  
```  
$ tree libs/array/test/  
libs/array/test/  
├── Jamfile  
├── test1  
│   ├── Jamfile  
│   └── array0.cpp  
├── test2  
│   ├── Jamfile  
│   └── array1.cpp  
└── test3  
    ├── Jamfile  
    ├── array2.cpp  
    └── print.hpp  
```  
  
where I deliberatelyhave broke syntax of the `test2/Jamfile` file:  
  
Next, I build the whole tree of the projects:  
  
```   
$ ./b2 libs/array/test  
libs/array/test/test2/Jamfile:2: syntax error at EOF  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
...found 267 targets...  
...updating 23 targets...  
gcc.compile.c++ bin.v2/libs/array/test/test3/array2.test/gcc-8/debug/threading-multi/visibility-hidden/array2.o  
gcc.compile.c++ bin.v2/libs/array/test/test1/array0.test/gcc-8/debug/threading-multi/visibility-hidden/array0.o  
gcc.link bin.v2/libs/array/test/test3/array2.test/gcc-8/debug/threading-multi/visibility-hidden/array2  
testing.capture-output bin.v2/libs/array/test/test3/array2.test/gcc-8/debug/threading-multi/visibility-hidden/array2.run  
**passed** bin.v2/libs/array/test/test3/array2.test/gcc-8/debug/threading-multi/visibility-hidden/array2.test  
gcc.link bin.v2/libs/array/test/test1/array0.test/gcc-8/debug/threading-multi/visibility-hidden/array0  
testing.capture-output bin.v2/libs/array/test/test1/array0.test/gcc-8/debug/threading-multi/visibility-hidden/array0.run  
**passed** bin.v2/libs/array/test/test1/array0.test/gcc-8/debug/threading-multi/visibility-hidden/array0.test  
...updated 23 targets...  
  
$ echo $?  
1  
```  
  
The projects `test1` and `test3` build with success, and the `test2` did not due to syntax error signalled before any build started (first line of the output). However, the final exit code of the `b2` is `1`.  
  
In my opinion, it is correct that the whole build fails (e.g. CI job is red) due to the non-zero exit code. It is just _unfortunate_ the syntax error is reported at the top of the output and not at the end, but I have no idea how this could be improved.  
  
Previously, in `b2` from Boost 1.72 you would get zero exit code for the situation above, but that obviously is not right, is it, due to the serious syntax error.  
  
Although I understand that change in behaviour may be surprising, I think it should be taken as an opportunity to correct any Jamfiles revealed as broken.  
  
I'm also not sure how the [-q option](https://boostorg.github.io/build/manual/develop/index.html#bbv2.overview.invocation.options) should behave here, but I suspect it only affects targets of scope of single project, and not the whole build.  
  
Perhaps @grafikrobot or @swatanabe may have some more helpful thoughts.

---

## Comment 4

> Username: userdocs  
> Created at: 2020-04-29 23:11:54 UTC  
> Url: https://github.com/boostorg/build/issues/585#issuecomment-621516587  

Thank you and i agree, the real issue it highlighted was to fix the local jam files.  
  
My concern here is one of the end user experience and i'll explain why.  
  
Let us say that we are just downloading and building `libtorrent` using `b2` for this example.  
  
Example 1: syntax error using generalised commands so you get the idea  
  
```  
git clone libtorrent  
cd libtorrent  
b2 ...  
  
...stuff  
  
Jamroot.jam:1: syntax error at EOF  
  
...stuff  
  
common.copy /root/qbittorrent-build/lib/libtorrent.a  
...updated 413 targets...  
root@server:~#  
```  
  
A apparently successful build unless we do this:  
  
```  
root@server:~# echo $?  
1  
```  
  
But this is the outcome  
  
```  
root@server:~# ldd qbittorrent-build/lib/libtorrent.a  
  
        not a dynamic executable  
		  
root@server:~# cksum qbittorrent-build/lib/libtorrent.a  
  
1664259237 23521960 qbittorrent-build/lib/libtorrent.a  
```  
  
Example 2: no syntax errors  
  
```  
git clone libtorrent  
cd libtorrent  
b2 ...  
  
...stuff  
  
common.copy /root/qbittorrent-build/lib/libtorrent.a  
...updated 413 targets...  
root@server:~#  
```  
  
A actual successful build with no exit errors:  
  
```  
root@server:~# echo $?  
0  
```  
  
But the final outcome to the end user us identical.  
  
```  
root@server:~# ldd qbittorrent-build/lib/libtorrent.a  
  
        not a dynamic executable  
		  
root@server:~# cksum qbittorrent-build/lib/libtorrent.a  
  
1664259237 23521960 qbittorrent-build/lib/libtorrent.a  
```  
  
How would we know a critical error occurred as the outcomes are the same? The checksums are the same `1664259237 23521960` and i can use this `libtorrent.a` to build and use qbittorrent with no issues.  
  
The only reason i noticed this was because my build is scripted and this exit was halting my build before completion. If i had simply been building libtorrent from git i would have been unaware of the issue as the exit has no obvious impact.  
  
So in this current state, it's almost an invisible error to anybody building libtorrent via a terminal.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:13 UTC  
> Url: https://github.com/boostorg/build/issues/585#issuecomment-850868297  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
