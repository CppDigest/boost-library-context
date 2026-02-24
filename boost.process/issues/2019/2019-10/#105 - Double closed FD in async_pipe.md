# #105 - Double closed FD in async_pipe [Open]

> Username: Ondrej-Vater  
> Created at: 2019-10-10 16:02:57 UTC  
> Updated at: 2019-10-10 16:23:10 UTC  
> Url: https://github.com/boostorg/process/issues/105  

I use `async_pipe` for communication between child and parent processes. When a child process finishes, then I destroy the `async_pipe`. The `async_pipe` destructor calls `::close()` on the both sides of the pipe, see:  
```  
    ~async_pipe()  
    {  
        if (_sink .native_handle()  != -1)  
            ::close(_sink.native_handle());  
        if (_source.native_handle() != -1)  
            ::close(_source.native_handle());  
    }  
```  
However the destructor of `boost::asio::posix::stream_descriptor` (the type of `_sink` and `_source`) calls the other `::close()` on the file descriptor if it isn't `-1`. A closed FD in the `async_pipe` destructor isn't set to `-1` anywhere. The other `::close()` is called `/usr/local/include/boost/asio/detail/impl/descriptor_ops.ipp:49` in `boost::asio::detail::descriptor_ops::close()`. Call stack:  
```  
boost::asio::detail::descriptor_ops::close descriptor_ops.ipp:49  
boost::asio::detail::reactive_descriptor_service::destroy reactive_descriptor_service.ipp:95  
boost::asio::basic_io_object<boost::asio::detail::reactive_descriptor_service, true>::~basic_io_object basic_io_object.hpp:247  
boost::asio::posix::descriptor::~descriptor descriptor.hpp:629  
boost::asio::posix::stream_descriptor::~stream_descriptor stream_descriptor.hpp:48  
boost::process::detail::posix::async_pipe::~async_pipe async_pipe.hpp:71  
```  
  
Consequences: If a system hasn't reused the closed FD, then the other `::close()` returns `EBADF` which is ignored. Otherwise the other `::close()` closes a FD of someone else.
