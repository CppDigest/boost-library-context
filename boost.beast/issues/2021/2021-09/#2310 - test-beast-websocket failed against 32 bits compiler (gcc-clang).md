# #2310 - test/beast/websocket failed against 32 bits compiler (gcc/clang) [Closed]

> Username: xing1xu  
> Created at: 2021-09-06 03:47:50 UTC  
> Updated at: 2024-01-01 09:12:08 UTC  
> Closed at: 2024-01-01 09:12:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2310  

With 64 mod.  
  
> boost_1_75_0/b2 toolset=clang address-model=64 --abbreviate-paths --disable-icu  --reconfigure -d+1 -d+2 cflags="-std=c++14  "  write  
  
Pass.  
  
With 32 mode.  
  
> boost_1_75_0/b2 toolset=clang address-model=32 --abbreviate-paths --disable-icu  --reconfigure -d+1 -d+2 cflags="-std=c++14  "  write  
/bin/sh: line 9: 4003719 Segmentation fault      (core dumped) "../../../../../bin.v2/libs/beast/test/beast/websocket/write.test/clng-lnx-14.0./dbg/adrs-mdl-32/thrdp-pthrd/thrd-mlt/vsblt-hdn/write" > "../../../../../bin.v2/libs/beast/test/beast/websocket/write.test/clng-lnx-14.0./dbg/adrs-mdl-32/thrdp-pthrd/thrd-mlt/vsblt-hdn/write.output" 2>&1 < /dev/null  
  
Failed.  
  
> $cat write.output  
> beast.websocket.write  
> EXIT STATUS: 139

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-09-06 05:39:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2310#issuecomment-913358034  

Thank you. I will investigate.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2310#issuecomment-1008220740  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:11:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2310#issuecomment-1256863009  

@xing1xu Can you share some more details about the issue?
