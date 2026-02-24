# #99 - stacktrace comes incomplete for a signal handler on Darwin-x86_64 [Closed]

> Username: dmorilha-twilio  
> Created at: 2020-09-02 13:01:35 UTC  
> Updated at: 2020-11-18 19:18:11 UTC  
> Closed at: 2020-11-18 19:18:10 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/99  

I am getting an incomplete stacktrace when I use `boost::stacktrace` 1.73.0 on a _Darwin Kernel Version 19.6.0_ for my signal handler.  
  
```  
Caught signal 6(SIGABRT)  
 0# signalHandler(int) in /tmp/build-darwin/net/test/net-tests  
 1# _sigtramp in /usr/lib/system/libsystem_platform.dylib  
```  
  
Here is what I get for the same code on Linux:  
  
```  
Caught signal 6(SIGABRT)  
 0# 0x000000000050BDF9 in ./build-linux/net/test/net-tests  
 1# 0x00007F0D79303060 in /lib/x86_64-linux-gnu/libc.so.6  
 2# gsignal in /lib/x86_64-linux-gnu/libc.so.6  
 3# abort in /lib/x86_64-linux-gnu/libc.so.6  
 4# 0x0000000000512989 in ./build-linux/net/test/net-tests  
 5# 0x0000000000512999 in ./build-linux/net/test/net-tests  
 6# 0x00000000005129A9 in ./build-linux/net/test/net-tests  
 7# 0x00000000005129C1 in ./build-linux/net/test/net-tests  
 8# 0x0000000001912884 in ./build-linux/net/test/net-tests  
 9# 0x0000000001904D62 in ./build-linux/net/test/net-tests  
10# 0x00000000018F3451 in ./build-linux/net/test/net-tests  
11# 0x00000000018F3D47 in ./build-linux/net/test/net-tests  
12# 0x00000000018F432D in ./build-linux/net/test/net-tests  
13# 0x00000000018FE762 in ./build-linux/net/test/net-tests  
14# 0x0000000001914EA4 in ./build-linux/net/test/net-tests  
15# 0x00000000019067A2 in ./build-linux/net/test/net-tests  
16# 0x00000000018FE29B in ./build-linux/net/test/net-tests  
17# 0x000000000050F971 in ./build-linux/net/test/net-tests  
18# 0x000000000050F80D in ./build-linux/net/test/net-tests  
19# 0x0000000000510317 in ./build-linux/net/test/net-tests  
20# 0x0000000000510277 in ./build-linux/net/test/net-tests  
21# 0x00000000005101C5 in ./build-linux/net/test/net-tests  
22# 0x000000000051014E in ./build-linux/net/test/net-tests  
23# 0x00000000005100E5 in ./build-linux/net/test/net-tests  
24# 0x000000000050FD7E in ./build-linux/net/test/net-tests  
25# 0x00007F0D79C43E6F in /usr/lib/x86_64-linux-gnu/libstdc++.so.6  
26# 0x00007F0D7A4574A4 in /lib/x86_64-linux-gnu/libpthread.so.0  
27# clone in /lib/x86_64-linux-gnu/libc.so.6  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-11-18 19:18:10 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/99#issuecomment-729897815  

This is due to different signal handling implementations on the target OSes. Signal handlers are not guaranteed to run on top of the active thread stack. See https://man7.org/linux/man-pages/man2/signal.2.html :  
```  
 The only portable use of signal() is to set a signal's disposition to  
 SIG_DFL or SIG_IGN.  The semantics when using signal() to establish a  
 signal handler vary across systems (and POSIX.1 explicitly permits  
 this variation); do not use it for this purpose.  
```
