# #138 fix addr2line for pie binaries [Merged]

> Username: ja2142  
> Created at: 2023-06-04 21:50:36 UTC  
> Updated at: 2023-08-27 18:35:21 UTC  
> Merged at: 2023-08-27 18:34:57 UTC  
> Closed at: 2023-08-27 18:34:57 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/138  

`addr2line` can't translate addresses from position independent binaries, it needs offsets from binary base. Here it's achieved by reading `/proc/self/maps` (to be honest, I don't know how portable exactly that is, but judging from the fact that `/proc/self/exe` is read, that is not a huge concern?).  
  
Most likely doesn't work with C++03, I can try to fix it if needed.  
  
I'm not sure, but it might fix #97 or #113.  
  
---  
  
There's a commented out line   
```  
//return addr2line("-Cfipe", addr); // Does not seem to work in all cases  
```  
It's likely that it'd work in more cases with my fix, if offsets from binary image base were passed to `addr2line` - in that case, I think it might be wise to just use it as it was before, and get both function name and file info from `addr2line`, as it seems to be more robust than `dladdr` (`addr2line` should work for all binaries with debug info, while `dl` functions probably only work for exported symbols (and non-pie binaries, for some reason); maybe `dladdr` should still be left as a fallback).

---

## Comment 1

> Username: coveralls  
> Created_at: 2023-07-18 10:50:35 UTC  
> Updated_at: 2023-08-12 11:34:13 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/138#issuecomment-1639991428  

[![Coverage Status](https://coveralls.io/builds/61948205/badge)](https://coveralls.io/builds/61948205)  
  
coverage: 91.376% (+0.4%) from 90.927% when pulling **988ee0f512d750b9c3a9808dc28f3a62fc6cdd18 on ja2142:addr2line_pie_fix** into **c6e771286801ca7f5f58b3d6a812b95b85e71bc0 on boostorg:develop**.

---

## Comment 2

> Username: raidenluikang  
> Created_at: 2023-07-19 02:28:58 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/138#issuecomment-1641289260  

It still write unusable addresses for stripped pie shared libraries. If I'm not mistaken.

---

## Comment 3

> Username: ja2142  
> Created_at: 2023-08-12 12:16:58 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/138#issuecomment-1675884016  

I just realized I've only fixed converting whole frame to string, `source_line()`, `source_file()` etc. probably still won't work for pie binaries. I'll try to get it fixed somewhen today.

---

## Comment 4

> Username: ja2142  
> Created_at: 2023-08-12 14:15:32 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/138#issuecomment-1675935113  

@raidenluikang well, exported symbols (I guess those that are visible in `objdump -T <binary>`?) should probably be visible, doesn't matter if binary is stripped or not (and they are in my simple tests). Non-exported functions would require debug symbols to have names in a stacktrace.

---

## Comment 5

> Username: apolukhin  
> Created_at: 2023-08-27 18:35:20 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/138#issuecomment-1694733037  

Many thanks for the great work!

---
