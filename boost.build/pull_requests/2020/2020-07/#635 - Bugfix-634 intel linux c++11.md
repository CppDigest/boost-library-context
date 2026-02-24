# #635 Bugfix/634 intel linux c++11 [Closed]

> Username: aminiussi  
> Created at: 2020-07-08 10:33:06 UTC  
> Updated at: 2021-10-02 21:01:45 UTC  
> Closed at: 2020-07-13 15:39:06 UTC  
> Url: https://github.com/boostorg/build/pull/635  

As indicated in #634 , the correct flags for Intel C++11 on linux is -std=c++11.  
  
I have no idea why the change in history.adoc is here and not in the boostorg repo.  
  
Anyway, the 2 lines in the build.sh script are what matters.

---

## Comment 1

> Username: ax3l  
> Created_at: 2020-07-12 19:24:18 UTC  
> Updated_at: 2020-07-12 19:30:18 UTC  
> Url: https://github.com/boostorg/build/pull/635#issuecomment-657264482  

There is a bit more to this, e.g. the [Intel oneAPI ICC](https://software.intel.com/content/www/us/en/develop/articles/oneapi-repo-instructions.html) activation scripts are now in  
```  
/opt/intel/inteloneapi/setvars.sh  
```  
  
> I have no idea why the change in history.adoc is here and not in the boostorg repo.  
  
Your branch is outdated, please rebase the branch to remove the confusing part. For example, assuming `boostorg` is listed in `git remote -v` as "mainline": `git fetch mainline; git rebase mainline/develop`.  
  
General question: are these changes sufficient for you to build with a recent ICC on Linux? Cannot reproduce that this already fixes all the outdated parts when trying this in https://github.com/alpaka-group/alpaka/pull/1066

---

## Review 2 [Commented]

> Username: Flamefire  
> Created_at: 2020-07-13 10:16:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/635#pullrequestreview-447120075  

📁 src/engine/build.sh

```diff
  83 |             intel-darwin) ( ${CXX:=icc} -xc++ check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;
  84 |-             intel-linux) ( ${CXX:=icc} -xc++ check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;
  84 |+             intel-linux) ( ${CXX:=icc} -std=c++11 check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;
```

> Username: Flamefire  
> Created_at: 2020-07-13 10:16:52 UTC  
> Updated_at: 2020-07-13 10:18:57 UTC  
> Url: https://github.com/boostorg/build/pull/635#discussion_r453546386  

Isn't the meaning of `-xc++` "Treat the source files as C++ (as opposed to C)" and not related to the standard?  Hence if at all the `-std=c++11` should be appended.  
  
But is there an actual failure due to this flag missing?

> Username: aminiussi  
> Created_at: 2020-07-13 10:37:59 UTC  
> Url: https://github.com/boostorg/build/pull/635#discussion_r453557726  

>isn't the meaning of -xc++ "Treat the source files as C++ (as opposed to C)" and not related to the standard?    
  
I don't know, it's not a recognized option of my linux icc.  I would have expected:  
```  
             intel-linux) ( ${CXX:=icpc} -std=c++11 check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;   
```  
The build error is connected to the fact that `icc -xc++` fails.  
  
> But is there an actual failure due to this flag missing?  
  
Yes, as some code (in test for example)  requires c++11 to be activated, which is not the case by default.

> Username: aminiussi  
> Created_at: 2020-07-13 10:42:34 UTC  
> Url: https://github.com/boostorg/build/pull/635#discussion_r453560084  

> Your branch is outdated, please rebase the branch to remove the confusing part. For example, assuming boostorg is listed in git remote -v as "mainline": git fetch mainline; git rebase mainline/develop.  
  
Thanks, I'll try that  
  
> General question: are these changes sufficient for you to build with a recent ICC on Linux?   
  
Yes  
  
> Cannot reproduce that this already fixes all the outdated parts when trying this in alpaka-group/alpaka#1066  
  
My Intel distribution is slightly older.  
  
```  
[alainm@castor ~]$ icpc -v  
icpc version 19.1.0.166 (gcc version 8.3.0 compatibility)  
[alainm@castor ~]$   
```

> Username: Flamefire  
> Created_at: 2020-07-13 10:49:59 UTC  
> Updated_at: 2020-07-13 10:52:09 UTC  
> Url: https://github.com/boostorg/build/pull/635#discussion_r453564086  

I've just ran `icc --help` on an icc I could get hold of and it does output `-x <type>`, so only thing missing is the space, although I tried it and it doesn't error out.  
  
Can you elaborate what you mean by "it's not a recognized option of my linux icc"? Is there an actual error when you use it on a test file? Does it really not show up in `icc --help` (it does for me)?  
  
I've also been able to successfully compile a C++11 source without any additional flag. That was `icc 18.0.1 20171018`. But with an older version (`icc 15.0.1 20141023`) it did require the `-std` flag.  
  
Hence I'd suggest to change this line:  
  
```diff  
-            intel-linux) ( ${CXX:=icc} -std=c++11 check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;  
+            intel-linux) ( ${CXX:=icc} -x c++ -std=c++11 check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;  
```  
  
and same at the other place.

> Username: aminiussi  
> Created_at: 2020-07-13 11:20:44 UTC  
> Url: https://github.com/boostorg/build/pull/635#discussion_r453579432  

> Can you elaborate what you mean by "it's not a recognized option of my linux icc"? Is there an actual error when you use it on a test file? Does it really not show up in icc --help (it does for me)?  
  
The -xc++ option (no space) is not in my documentation. In my manpage  `-x<something>` refers to code generation (where something is  an architecture like **BROADWELL** or **COMMON-AVX512**).  
  
I tried both on a dummy source file and there was no error. The build (bootstrap.sh actually) failed on a c++11 construct that I misattributed apparently.  
  
As for C++11 compatibility, there are two aspects: the library selection, based on the underlying g++ distribution, the dialect selection, which is impacted by the `-std=...` option (default can cover the standard in various way according to distributions I guess).   
  
I'm not sure which the script insist on using icc instead of icpc which is the normal Intel's C++ compiler name.

> Username: Flamefire  
> Created_at: 2020-07-13 11:32:57 UTC  
> Url: https://github.com/boostorg/build/pull/635#discussion_r453585433  

Yes because the option is documented as `-x <type>`. `icc` is used instead of `icpc` presumably for mirroring behavior of GCC and `icc` is fully able to compile C++ code. Either by checking the file extension or by `-x c++`.  
  
So I guess if you rebase your branch, fix the `-xc++` to `-x c++' typo and add (not replace) `-std=c++11` then this PR can be accepted.

> Username: aminiussi  
> Created_at: 2020-07-13 11:47:08 UTC  
> Url: https://github.com/boostorg/build/pull/635#discussion_r453592579  

> icc is used instead of icpc presumably for mirroring behavior of GCC   
  
But with gcc, g++ is used:  
> gcc) ( ${CXX:=g++} -x c++ -std=c++11 check_cxx11.cpp && rm -f a.out ) 1>/dev/null 2>/dev/null ;;

> Username: Flamefire  
> Created_at: 2020-07-13 12:11:00 UTC  
> Updated_at: 2020-07-13 12:11:01 UTC  
> Url: https://github.com/boostorg/build/pull/635#discussion_r453604173  

Then it was likely overlooked in https://github.com/boostorg/build/commit/0ec79867fd4605738cfea3333f057ae02dfd7417 which changed gcc to g++ and similar. I guess it is fine to change icc to icpc then

> Username: ax3l  
> Created_at: 2020-07-13 17:40:45 UTC  
> Updated_at: 2020-07-13 17:41:07 UTC  
> Url: https://github.com/boostorg/build/pull/635#discussion_r453819184  

Yes, the Intel C++ compiler is technically `icpc`. The C compiler `icc` does recognize C++ files and forwards, but technically we should call `icpc`.


---

## Comment 3

> Username: aminiussi  
> Created_at: 2020-07-13 15:35:18 UTC  
> Url: https://github.com/boostorg/build/pull/635#issuecomment-657631778  

The rebase has too many conficts. I'll start from a fresh repository

---

## Comment 4

> Username: aminiussi  
> Created_at: 2020-07-13 15:39:06 UTC  
> Url: https://github.com/boostorg/build/pull/635#issuecomment-657633861  

Follow up in #637

---

## Comment 5

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:44 UTC  
> Url: https://github.com/boostorg/build/pull/635#issuecomment-932819955  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
