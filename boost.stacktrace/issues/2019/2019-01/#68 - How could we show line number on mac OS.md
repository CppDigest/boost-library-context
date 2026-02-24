# #68 - How could we show line number on mac OS? [Closed]

> Username: wind2412  
> Created at: 2019-01-01 11:01:47 UTC  
> Updated at: 2024-09-12 08:22:25 UTC  
> Closed at: 2024-09-12 08:22:25 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/68  

We don't have `addr2line` on macos. I wonder how we can get line information use our macro configuration. I used `brew` to install `boost`, and the version is `1.69.0`.  
  
After `boost::stacktrace::stacktrace()`, I only get this output:  
  
```  
 0# run() in /Users/xxx/Desktop/./bin/test  
 1# main in /Users/xxx/Desktop/./bin/test  
```  
  
Thanks in advance :)

---

## Comment 1

> Username: ivanarh  
> Created at: 2019-01-10 20:49:33 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/68#issuecomment-453248285  

atos may help  
https://www.unix.com/man-page/osx/1/atos/

---

## Comment 2

> Username: narodnik  
> Created at: 2020-07-21 07:41:23 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/68#issuecomment-661691865  

See also #97 we get on linux too

---

## Comment 3

> Username: spinicist  
> Created at: 2021-01-11 11:58:27 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/68#issuecomment-757905991  

Hello, did anyone manage to resolve this? Address Sanitizer on Mac manages to get line numbers.

---

## Comment 4

> Username: apolukhin  
> Created at: 2024-09-12 08:22:25 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/68#issuecomment-2345583910  

Was probably fixed in https://github.com/boostorg/stacktrace/commit/fbcd543b5180b8ee307fef6fbfc34aa372d330cf  
  
If not - please reopen the issue
