# #180 - Possibility to print decodable addresses with ASLR anabled [Closed]

> Username: McCzarny  
> Created at: 2024-09-07 15:28:19 UTC  
> Updated at: 2025-01-07 20:59:50 UTC  
> Closed at: 2025-01-07 20:59:48 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/180  

Hello, I was trying to use the library to log backtraces so they could be later decoded using the binary. I've noticed that when Address Space Layout Randomization is enabled, I'm getting different address on each run:  
```  
$ ./build/Debug/boost-stacktrace-address-randomization   
 0# 0x000055655F1BB761 in ./build/Debug/boost-stacktrace-address-randomization  
 1# 0x000055655F1BB7DE in ./build/Debug/boost-stacktrace-address-randomization  
 2# 0x000055655F1BB7EE in ./build/Debug/boost-stacktrace-address-randomization  
 3# 0x00007FDBA333FD90 in /lib/x86_64-linux-gnu/libc.so.6  
 4# __libc_start_main in /lib/x86_64-linux-gnu/libc.so.6  
 5# 0x000055655F1BB645 in ./build/Debug/boost-stacktrace-address-randomization  
  
$ ./build/Debug/boost-stacktrace-address-randomization   
 0# 0x0000560C98DD3761 in ./build/Debug/boost-stacktrace-address-randomization  
 1# 0x0000560C98DD37DE in ./build/Debug/boost-stacktrace-address-randomization  
 2# 0x0000560C98DD37EE in ./build/Debug/boost-stacktrace-address-randomization  
 3# 0x00007FAAD04E7D90 in /lib/x86_64-linux-gnu/libc.so.6  
 4# __libc_start_main in /lib/x86_64-linux-gnu/libc.so.6  
 5# 0x0000560C98DD3645 in ./build/Debug/boost-stacktrace-address-randomization  
```  
  
It seems that I can still decode the addresses if I get `/proc/self/maps` and subtract the base address from the generated stacktrace. I've noticed that there is a similar logic when `addr2line` flavor is enabled.  
  
- Is there a way to get backtrace with addresses matching the binary?  
- If not, is it worth to implement it? I could try to provide the implementation.  
  
I'm aware that the work on the libarary may be limited because of C++23, but still there is a lot projects where it's easier to use a newer version of boost than upgrade a compiler.

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-09-12 07:41:24 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/180#issuecomment-2345499146  

> If not, is it worth to implement it? I could try to provide the implementation.  
  
Yes, please give it a try

---

## Comment 2

> Username: McCzarny  
> Created at: 2024-09-22 12:48:42 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/180#issuecomment-2366772001  

@apolukhin So far the code change needed to make it work on unix is quite simple but I have some difficulties with deciding how to provide a way to enable the new behavior. I will appreciate any help here.  
  
The thing is that it can work with every mode like `_BASIC` , `_ADDR2LINE`, `_BACKTRACE`, so I think that there is no point with creating a new library variant. The easiest option would be to define a new macro flag, but it would require a rebuild to change the way of address printing for projects that links to `boost_stacktrace_*`. Alternatively I could add a new type, which will require more code. What do you think?  
  
https://github.com/McCzarny/stacktrace/commit/ec6066f3444b9e99f42194b2ba44164d0516dfdb

---

## Comment 3

> Username: McCzarny  
> Created at: 2024-12-13 09:35:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/180#issuecomment-2540992926  

@apolukhin , I would like to return to this feature, but still I have no good idea to make it configurable. Any input would be very helpful ;)

---

## Comment 4

> Username: apolukhin  
> Created at: 2024-12-22 16:08:51 UTC  
> Updated at: 2024-12-22 16:09:16 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/180#issuecomment-2558504891  

Why not just turn it on by default?  
  
Make PR and let's decide it there

---

## Comment 5

> Username: apolukhin  
> Created at: 2025-01-07 20:59:48 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/180#issuecomment-2576210082  

Fixed in https://github.com/boostorg/stacktrace/commit/b170b28479d34467896195f6ebc754687bcc2036
