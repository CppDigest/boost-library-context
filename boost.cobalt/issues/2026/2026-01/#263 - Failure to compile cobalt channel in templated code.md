# #263 - Failure to compile cobalt channel in templated code. [Open]

> Username: 17steen  
> Created at: 2026-01-27 11:44:06 UTC  
> Updated at: 2026-01-27 19:41:11 UTC  
> Url: https://github.com/boostorg/cobalt/issues/263  

I'm trying to use cobalt in conjunction with open62451pp to make calling asynchronous function more linear.  
  
I've implemented a template like such:  
  
```cpp  
template <typename T>  
struct AsyncResult<UseCobaltToken, T> {  
  
    template <typename Initiation, typename... Args>  
    static auto initiate(Initiation&& initiation, UseCobaltToken, Args&&... args) -> boost::cobalt::task<T> {  
  
        auto ex = co_await boost::cobalt::this_coro::executor;  
  
        auto channel = boost::cobalt::channel<T>(1u, ex);  
  
        auto callback = [&channel, ex](auto&& res) mutable {  
            boost::cobalt::spawn(ex,  
                    [](auto& chan, auto response) mutable -> boost::cobalt::task<void> {  
                        co_await chan.write(std::move(response));  
                    }(channel, std::forward<decltype(res)>(res)),  
                    boost::asio::detached  
                );  
        };  
  
        std::invoke(std::forward<Initiation>(initiation), std::move(callback), std::forward<Args>(args)...);  
  
        co_return co_await channel.read();  
    }  
};  
```  
But when compiling, i get the following issue:  
  
```  
[2/2] Linking CXX executable play  
FAILED: [code=1] play   
: && /usr/bin/clang++-21 -g -fuse-ld=lld CMakeFiles/play.dir/main.cpp.o -o play  vcpkg_installed/x64-linux-clang21/debug/lib/libfmtd.a  vcpkg_installed/x64-linux-clang21/debug/lib/libboost_cobalt.a  vcpkg_installed/x64-linux-clang21/debug/lib/libopen62541pp.a  vcpkg_installed/x64-linux-clang21/debug/lib/libboost_date_time.a  vcpkg_installed/x64-linux-clang21/debug/lib/libboost_container.a  vcpkg_installed/x64-linux-clang21/debug/lib/libboost_context.a  vcpkg_installed/x64-linux-clang21/debug/lib/libopen62541.a  -lpthread  -lm  -lrt && :  
ld.lld: error: undefined symbol: boost::asio::detail::cancellation_handler<boost::cobalt::channel<opcua::Result<opcua::Variant>>::write_op::cancel_impl>::call(boost::asio::cancellation_type)  
>>> referenced by main.cpp  
>>>               CMakeFiles/play.dir/main.cpp.o:(vtable for boost::asio::detail::cancellation_handler<boost::cobalt::channel<opcua::Result<opcua::Variant>>::write_op::cancel_impl>)  
  
ld.lld: error: undefined symbol: boost::asio::detail::cancellation_handler<boost::cobalt::channel<opcua::Result<opcua::Variant>>::write_op::cancel_impl>::destroy()  
>>> referenced by main.cpp  
>>>               CMakeFiles/play.dir/main.cpp.o:(vtable for boost::asio::detail::cancellation_handler<boost::cobalt::channel<opcua::Result<opcua::Variant>>::write_op::cancel_impl>)  
clang++-21: error: linker command failed with exit code 1 (use -v to see invocation)  
```  
  
I'm compiling with clang++-21 and linking with lld, although, linking with ld also failed.  
  
Other uses of the channel in non templated code, following the example code did compile:  
(It's using the same Type when instanciating the channel).  
  
```  
    auto ex = co_await boost::cobalt::this_coro::executor;  
  
    auto chan = std::make_shared<boost::cobalt::channel<opcua::Result<opcua::Variant>>>(1u, ex);  
  
    opcua::services::readValueAsync(client, node,  
        [chan, ex](opcua::Result<opcua::Variant> res) {  
            fmt::println("Received value: {}, executing coroutine.", res.hasValue() ? "Success" : "Error");  
            boost::cobalt::spawn(ex,  
                [chan, r = std::move(res)](this auto) -> boost::cobalt::task<void> {  
                    co_await chan->write(std::move(r));  
                }(),  
                boost::asio::detached  
            );  
    });  
```  
  
Maybe it's a compiler bug on my end? I'm not sure.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2026-01-27 17:32:51 UTC  
> Url: https://github.com/boostorg/cobalt/issues/263#issuecomment-3806529626  

I have no idea, but they're sadly common with coroutines. Did you check what gcc does?

---

## Comment 2

> Username: 17steen  
> Created at: 2026-01-27 19:41:11 UTC  
> Url: https://github.com/boostorg/cobalt/issues/263#issuecomment-3807160701  

Hello Klemens, I tried to compile with gcc, but i couldn't get a recent enough version on my computer, i believe i tried with gcc 15.1 but some other thing failed to compile when i tried that.  
  
I believed that I was covered in terms of coroutines by using the latest llvm release (21).
