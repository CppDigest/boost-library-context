# #111 - boost::process::opstream throws exception in destructor [Closed]

> Username: sorayuki  
> Created at: 2019-11-27 03:06:14 UTC  
> Updated at: 2023-06-28 12:24:17 UTC  
> Closed at: 2023-06-28 12:24:17 UTC  
> Url: https://github.com/boostorg/process/issues/111  

Boost 1.71  
MacOS 10.15 x64  
XCode, Clang  
  
Program build with CMake, with `set(CMAKE_CXX_STANDARD 11)`  
  
When sub-process exited ( for example, a MPV player, which read FLV stream from stdin, exited due to user pressed Q ), boost::process::opstream can't destruct successfully because C++ 11 make noexcept(true) default to destructor.  
  
Since `boost::process::opstream` is inherited from `std::basic_stream`, and `std::basic_stream::~basic_stream` has no `noexcept(true)`, so the compiler stops me from add `noexcept(false)` to `boost::process::basic_opstream::~basic_opstream`.  
  
I have no idea how to fix it.  
  
```  
(lldb) bt  
* thread #4, stop reason = step in  
  * frame #0: 0x0000000100011a3d rtmp-test-server`boost::process::detail::throw_last_error() at config.hpp:86:5  
    frame #1: 0x00000001000119d2 rtmp-test-server`boost::process::detail::posix::basic_pipe<char, std::__1::char_traits<char> >::write(this=0x000070000eb58928, data="...", count=369) at basic_pipe.hpp:87:13  
    frame #2: 0x00000001000118f1 rtmp-test-server`boost::process::basic_pipebuf<char, std::__1::char_traits<char> >::_write_impl(this=0x000070000eb588e8) at pipe.hpp:270:36  
    frame #3: 0x0000000100011045 rtmp-test-server`boost::process::basic_pipebuf<char, std::__1::char_traits<char> >::sync(this=0x000070000eb588e8) at pipe.hpp:189:40  
    frame #4: 0x0000000100011336 rtmp-test-server`boost::process::basic_pipebuf<char, std::__1::char_traits<char> >::overflow(this=0x000070000eb588e8, ch=-1) at pipe.hpp:184:18  
    frame #5: 0x0000000100010ee6 rtmp-test-server`boost::process::basic_pipebuf<char, std::__1::char_traits<char> >::~basic_pipebuf(this=0x000070000eb588e8) at pipe.hpp:127:17  
    frame #6: 0x0000000100010e85 rtmp-test-server`boost::process::basic_pipebuf<char, std::__1::char_traits<char> >::~basic_pipebuf(this=0x000070000eb588e8) at pipe.hpp:125:5  
    frame #7: 0x0000000100010dd4 rtmp-test-server`boost::process::basic_opstream<char, std::__1::char_traits<char> >::~basic_opstream(this=0x000070000eb588e0, vtt=0x00000001000f6448) at pipe.hpp:407:7  
    frame #8: 0x0000000100008410 rtmp-test-server`boost::process::basic_opstream<char, std::__1::char_traits<char> >::~basic_opstream(this=0x000070000eb588e0) at pipe.hpp:407:7  
    frame #9: 0x00000001000071e4 rtmp-test-server`launch(port=56300, icb=0x00007ffeefbff958, setstate=function<void (const char *)> @ 0x000070000eb58ee0)>) at main.cpp:199:5  
    frame #10: 0x000000010004201c rtmp-test-server`main::$_3::operator(this=0x00000001052324f8)() const at main.cpp:318:44  
    frame #11: 0x0000000100041e9d rtmp-test-server`decltype(__f=0x00000001052324f8)()) std::__1::__invoke<main::$_3>(main::$_3&&) at type_traits:4361:1  
    frame #12: 0x0000000100041e05 rtmp-test-server`void std::__1::__thread_execute<std::__1::unique_ptr<std::__1::__thread_struct, std::__1::default_delete<std::__1::__thread_struct> >, main::$_3>(__t=size=2, (null)=__tuple_indices<> @ 0x000070000eb58f58) at thread:342:5  
    frame #13: 0x0000000100041696 rtmp-test-server`void* std::__1::__thread_proxy<std::__1::tuple<std::__1::unique_ptr<std::__1::__thread_struct, std::__1::default_delete<std::__1::__thread_struct> >, main::$_3> >(__vp=0x00000001052324f0) at thread:352:5  
    frame #14: 0x00007fff714dbd36 libsystem_pthread.dylib`_pthread_start + 125  
    frame #15: 0x00007fff714d858f libsystem_pthread.dylib`thread_start + 15  
(lldb)  
```

---

## Comment 1

> Username: sehe  
> Created at: 2023-06-10 12:26:35 UTC  
> Url: https://github.com/boostorg/process/issues/111#issuecomment-1585646381  

Adding noexcept will not change anything, as it is already the default as you mention.  
  
The problem is that the destructor syncs - which throws e.g. on broken pipe. Worse, it seems that it still fails after `close()` on the pipe. That seems to be a bug, to me as `~basic_pipebuf` says  
  
```c++  
    ~basic_pipebuf()  
    {  
        if (basic_pipebuf::is_open())  
            basic_pipebuf::overflow(Traits::eof());  
    }  
```  
  
Which implies that a prior `basic_opstream::close()` didn't close the `basic_pipebuf` instance. What's worse, I can't even seem to forcibly do that, even after  
```c++          
      os.close(); // os is boost::process::opstream  
      os.rdbuf()->close();  
      assert(!os.rdbuf()->is_open()); // FAILS  
```  
The failing assert also implies, that even as a hack we cannot forcibly reopen the pipe to avoid process termination, because `open()` doesn't do anything if the pipe is already open  
  
Since this problem unconditionally terminates the host process, I think it should be a high priority issue.
