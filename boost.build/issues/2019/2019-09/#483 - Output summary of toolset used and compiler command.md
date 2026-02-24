# #483 - [feature] Output summary of toolset used and compiler command [Open]

> Username: mloskot  
> Created at: 2019-09-18 09:11:40 UTC  
> Updated at: 2021-06-11 01:55:23 UTC  
> Url: https://github.com/boostorg/build/issues/483  

(Initially discussed on https://cpplang.slack.com/archives/C27KZLB0X/p1568727581248500)  
  
I think dumping toolset and compiler command in the configuration summary at the top of the `b2` output would be already a big usability improvement, clearly indicating what has been determined, what is being used, e.g.  
  
```  
$ b2 toolset=clang-7  
Summary  
  
    - toolset                  : clang-7  
    - compiler                 : /usr/bin/clang++  
  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
...patience...  
```  
  
Having the actual compiler command displayed is very useful.  
It would also allow a less experienced user of `b2` to catch issues (confusions) like this  
  
```  
b2 toolset=clang-6  
b2 toolset=clang-7  
b2 toolset=clang-8  
```  
  
where all builds use the very same `clang++`.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-11 01:54:43 UTC  
> Url: https://github.com/boostorg/build/issues/483#issuecomment-859203193  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
