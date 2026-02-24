# #142 - Crash on resuming cancelled push generators [Closed]

> Username: daumantas-kavolis-sensmetry  
> Created at: 2023-12-05 14:11:45 UTC  
> Updated at: 2023-12-05 23:39:43 UTC  
> Closed at: 2023-12-05 23:39:43 UTC  
> Url: https://github.com/boostorg/cobalt/issues/142  

Seems like cancelling push generators destroys associated receiver but the generator is still resumed, resulting in segfault/sanitizer errors.  
  
```cpp  
#include <boost/cobalt/channel.hpp>  
#include <boost/cobalt/generator.hpp>  
#include <boost/cobalt/main.hpp>  
  
using Channel = boost::cobalt::channel<int>;  
  
static auto source_gen(Channel &channel) -> boost::cobalt::generator<int> {  
  while (channel.is_open()) { co_yield co_await channel.read(); }  
}  
  
static auto sink_gen(Channel &channel) -> boost::cobalt::generator<boost::system::error_code, int> {  
  while (channel.is_open()) {  
    auto message = co_yield boost::system::error_code{};  
    //             ^ when resuming a cancelled generator crashes here due to `generator_promise::receiver ==  
    //                nullptr`  
    co_await channel.write(std::move(message));  
  }  
}  
  
auto co_main([[maybe_unused]] int argc, [[maybe_unused]] char *argv[]) -> boost::cobalt::main {  
  Channel channel;  
  auto source = source_gen(channel);  
  auto sink = sink_gen(channel);  
  
  co_await sink(42);  
}  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-12-05 15:58:16 UTC  
> Url: https://github.com/boostorg/cobalt/issues/142#issuecomment-1841083425  

Thanks for reporting that, I can reproduce it. I hope to have a fix tomorrow so it can be in the upcoming release.
