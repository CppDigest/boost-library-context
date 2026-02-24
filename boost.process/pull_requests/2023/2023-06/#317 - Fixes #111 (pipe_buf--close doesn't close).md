# #317 Fixes #111 (pipe_buf::close doesn't close) [Closed]

> Username: sehe  
> Created at: 2023-06-10 14:27:53 UTC  
> Updated at: 2024-10-24 23:21:59 UTC  
> Closed at: 2024-10-24 23:21:59 UTC  
> Url: https://github.com/boostorg/process/pull/317  

In response to issue #111  
  
This doesn't fundamentally change the behaviour, i.e. the destructor of `basic_pipe` (and by extension `basic_opstream` etc) will still throw when flushing to a peer that has closed.  
  
However, it gives users of Boost Process a way to avoid this, by `close()`-ing the stream/buffer/pipe before the destructor runs and handling any exceptions there.  
  
E.g.  
  
```c++  
bp::opstream _os;  
  
~MySafeDtor() {  
   try {  
       _os.close();  
   } catch(std::exception const& e) {  
       // exception-safe logging here  
   }  
  
   assert(!_os.rdbuf()->is_open()); // without this patch, this assert fails, causing certain program termination later  
}  
```

---
