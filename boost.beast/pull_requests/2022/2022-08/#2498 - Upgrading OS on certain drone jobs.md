# #2498 Upgrading OS on certain drone jobs [Closed]

> Username: sdarwin  
> Created at: 2022-08-11 22:17:52 UTC  
> Updated at: 2022-08-24 20:45:12 UTC  
> Closed at: 2022-08-24 20:45:12 UTC  
> Url: https://github.com/boostorg/beast/pull/2498  

Change to Ubuntu 22.04 and newer compiler on asan, tsan, and ubsan tests.  When running earlier tests, tsan had reported "Segmentation fault". After the upgrade, the output is more informative:   
```  
====== BEGIN OUTPUT ======  
beast.http.read  
ThreadSanitizer:DEADLYSIGNAL  
==11100==ERROR: ThreadSanitizer: SEGV on unknown address 0x60000212fff8 (pc 0x7f07c86c0880 bp 0x7f07c58bde90 sp 0x7f07c58bde68 T11102)  
==11100==The signal is caused by a WRITE memory access.  
ThreadSanitizer:DEADLYSIGNAL  
ThreadSanitizer: nested bug in the same thread, aborting.  
EXIT STATUS: 66  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-08-11 22:43:14 UTC  
> Url: https://github.com/boostorg/beast/pull/2498#issuecomment-1212566277  

![pullrequest](https://2498.beast.tracing.cppalliance.org/pullrequest-016ffb73.png)  
Timeline tracing charts: [https://2498.beast.tracing.cppalliance.org/index.html](https://2498.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-08-12 19:12:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2498#issuecomment-1213434916  

![pullrequest](https://2498.beast.tracing.cppalliance.org/pullrequest-95830f56.png)  
Timeline tracing charts: [https://2498.beast.tracing.cppalliance.org/index.html](https://2498.beast.tracing.cppalliance.org/index.html)

---
