# #448 - `v2::process_stdio` prematurely closes file descriptors when moved-from instance goes out of scope [Closed]

> Username: jgreitemann  
> Created at: 2025-01-09 17:10:44 UTC  
> Updated at: 2025-01-13 00:13:35 UTC  
> Closed at: 2025-01-13 00:13:34 UTC  
> Url: https://github.com/boostorg/process/issues/448  

On POSIX, the `v2::detail::process_io_binding` members of `v2::process_stdio` do not implement move semantics correctly: The underlying file descriptors are closed in the destructor if the `fd_needs_closing` flag is set, but neither the file descriptor, nor this flag, are reset when `process_io_binding` is moved.  
https://github.com/boostorg/process/blob/b529769eb5d08d9f95b0130fd2f1a49acad330f4/include/boost/process/v2/stdio.hpp#L162-L166  
  
I've encountered this as a problem when writing tests for some code that launches a process and reads some data from stdout. I wanted to avoid actually launching said process in tests, so I came up with a `FakeProcess` which I intend to be a drop-in replacement for `v2::process` in a generic wrapper. This is a small example demonstrating the issue:  
  
```cpp  
auto read_msg(boost::asio::io_context& context,  
              boost::asio::readable_pipe read_pipe)  
    -> boost::asio::awaitable<boost::system::error_code> {  
    std::string s;  
    boost::system::error_code ec;  
    co_await boost::asio::async_read_until(  
        read_pipe, boost::asio::dynamic_buffer(s), '\u0004',  
        boost::asio::redirect_error(boost::asio::use_awaitable, ec));  
    co_return ec;  
}  
  
auto write_msg(boost::asio::writable_pipe write_pipe)  
    -> boost::asio::awaitable<void> {  
    co_await boost::asio::async_write(write_pipe,  
                                      boost::asio::buffer("Hello\u0004"));  
}  
  
struct FakeProcess {  
    FakeProcess(boost::asio::io_context& context,  
                boost::process::v2::process_stdio stdio)  
        : _stdio{std::move(stdio)}  
    {  
        // Comment out the line below to "fix" the problem  
        // stdio.out.fd_needs_closing = false;  
  
        boost::asio::writable_pipe write_pipe{context, _stdio.out.fd};  
        boost::asio::co_spawn(context, write_msg(std::move(write_pipe)),  
                              boost::asio::detached);  
    }  
  
   private:  
    boost::process::v2::process_stdio _stdio;  
};  
  
int main() {  
    boost::asio::io_context context;  
    boost::asio::readable_pipe read_pipe{context};  
    boost::process::v2::process_stdio stdio{.in = {}, .out = read_pipe, .err = {}};  
  
    FakeProcess p(context, std::move(stdio));  
  
    auto future = boost::asio::co_spawn(context,  
                                        read_msg(context, std::move(read_pipe)),  
                                        boost::asio::use_future);  
    context.run();  
    std::puts(future.get().message().c_str());  
}  
```  
As is, this code prints "End of file" because the pipe is closed prematurely when `stdio` is moved into the member `_stdio`. [(Godbolt link)](https://godbolt.org/z/zTM75avEv)  
  
Commenting out the line in the constructor of `FakeProcess` fixes the issue by toggling the flag in the moved-from object, thereby "defusing" its destructor. Clearly, that's just a dirty workaround (made possible by `process_io_binding`'s lack of encapsulation).
