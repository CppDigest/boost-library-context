# #7 - Stack traces should include source filenames and line numbers when available [Closed]

> Username: eyalroz  
> Created at: 2016-11-22 11:34:51 UTC  
> Updated at: 2016-12-10 09:50:08 UTC  
> Closed at: 2016-12-10 09:50:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/7  

A typical stack trace with the library, even when you've defined `BOOST_STACKTRACE_USE_LIBUNWIND` and linked with `-lunwind`, is far from being specific enough:  
```  
Backtrace:  
 0# traced::traced() +0x28  
 1# with_trace<std::logic_error>::with_trace<char const (&) [44]>(char const (&) [44]) +0x3E  
 2# oops(int) +0x60  
 3# bar(int) +0x73  
 4# foo(int) +0x19  
 5# bar(int) +0x67  
 6# foo(int) +0x19  
 7# bar(int) +0x67  
 8# foo(int) +0x19  
 9# bar(int) +0x67  
10# foo(int) +0x19  
11# bar(int) +0x67  
12# foo(int) +0x19  
13# bar(int) +0x67  
14# foo(int) +0x19  
15# main +0x13  
16# __libc_start_main +0xF0  
17# _start +0x29  
18#   
```  
How do I know what these offsets mean? where are the source filenames and line  numbers?  
  
When you compile code with -g, the binary has information about positions in source files of different instructions. That's why something like libSegfault [can work](http://stackoverflow.com/a/6599348/1593077) and be used to produce more proper traces, e.g.  
```  
Backtrace:  
/lib/libSegFault.so[0xb7f9e100]  
??:0(??)[0xb7fa3400]  
/usr/include/c++/4.3/bits/stl_queue.h:226(std::queue<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::deque<std::basic_string<char, std::char_traits<char>, std::allocator<char> >, std::allocator<std::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >::push(std::basic_string<char, std::char_traits<char>, std::allocator<char> > const&))[0x805647a]  
/home/dbingham/src/middle-earth-mud/alpha6/src/engine/player.cpp:73(Player::input(std::basic_string<char, std::char_traits<char>, std::allocator<char> >))[0x805377c]  
/home/dbingham/src/middle-earth-mud/alpha6/src/engine/socket.cpp:159(Socket::Read())[0x8050698]  
/home/dbingham/src/middle-earth-mud/alpha6/src/engine/socket.cpp:413(ServerSocket::Read())[0x80507ad]  
/home/dbingham/src/middle-earth-mud/alpha6/src/engine/socket.cpp:300(ServerSocket::poll())[0x8050b44]  
/home/dbingham/src/middle-earth-mud/alpha6/src/engine/main.cpp:34(main)[0x8049a72]  
/lib/tls/i686/cmov/libc.so.6(__libc_start_main+0xe5)[0xb7d1b775]  
/build/buildd/glibc-2.9/csu/../sysdeps/i386/elf/start.S:122(_start)[0x8049801]  
  
```  
(that's after C++ name demangling). I think that's the kind of trace that developers would expect...

---

## Comment 1

> Username: apolukhin  
> Created at: 2016-11-22 20:15:39 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/7#issuecomment-262353186  

Do you have a portable POSIX non-GPL licensed solution by any chance? I'd appreciate a pull request.

---

## Comment 2

> Username: eyalroz  
> Created at: 2016-11-22 21:00:30 UTC  
> Updated at: 2016-11-22 21:20:58 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/7#issuecomment-262364149  

The best I have is links to the (documented-by-many-comments) glibc implementation:  
  
https://sourceware.org/git/?p=glibc.git;a=blob;f=debug/segfault.c  
https://sourceware.org/git/?p=glibc.git;a=blob;f=debug/backtracesyms.c  
https://sourceware.org/git/?p=glibc.git;a=blob;f=debug/backtracesymsfd.c  
  
I have no idea about the degree of portability or standard-compliance of this thing. It has some linux-specific parts, but I think they're not related to the actual backtrace,  just the memory map dump.  
  
Unfortuantely, I don't have the time to work on this in the near future; I've resigned to my fate of having to use gdb for my backtraces.  
  
**Edit:** Created a [question](http://stackoverflow.com/q/40751832/1593077) about this matter on [StackOverflow](http://stackoverflow.com/).

---

## Comment 3

> Username: apolukhin  
> Created at: 2016-12-10 09:50:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/7#issuecomment-266194115  

Fixed that
