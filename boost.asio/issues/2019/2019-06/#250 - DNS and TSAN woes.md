# #250 - DNS and TSAN woes [Closed]

> Username: cmazakas  
> Created at: 2019-06-04 15:38:06 UTC  
> Updated at: 2020-12-30 01:06:01 UTC  
> Closed at: 2020-12-30 01:06:00 UTC  
> Url: https://github.com/boostorg/asio/issues/250  

The following code seems able to cause tsan to segfault on osx with clang-6 and also on Linux with gcc-7 and clang-8.  
  
Are we not allowed to simply do DNS calls in parallel?  
  
```c++  
#include <boost/asio.hpp>  
#include <boost/asio/spawn.hpp>  
#include <thread>  
#include <vector>  
#include <string>  
  
  namespace asio = boost::asio;  
  
  int  
  main()  
  {  
    auto const urls =  
      std::vector<std::string>{"www.google.com", "www.facebook.com",  "www.youtube.com",  
                               "www.yahoo.com",  "www.wikipedia.org", "www.twitter.com",  
                               "www.amazon.com", "www.google.com",    "www.bing.com"};  
  
    auto const num_threads = 8;  
  
    asio::io_context io(num_threads);  
  
    auto const num_requests = urls.size() * num_threads;  
  
    std::atomic_int num_valid_responses{0};  
  
    auto resolvers = std::vector<asio::ip::tcp::resolver>();  
    resolvers.reserve(num_threads);  
    for (auto idx = 0; idx < num_threads; ++idx) { resolvers.emplace_back(asio::ip::tcp::resolver(io)); }  
  
    auto const crawler = [&io, &urls, &resolvers, num_requests](int idx) {  
      return [&io, &urls, &resolvers, num_requests, idx](asio::yield_context yield) -> void {  
        for (auto const url : urls) {  
          auto& resolver = resolvers[idx];  
          auto const endpoints = resolver.async_resolve(url, "http", yield);  
        }  
      };  
    };  
  
    auto threads = std::vector<std::thread>();  
    threads.reserve(num_threads);  
  
    using strand_type = asio::strand<typename asio::io_context::executor_type>;  
  
    auto strands = std::vector<strand_type>();  
    strands.reserve(num_threads);  
    for (auto idx = 0; idx < num_threads; ++idx) {  
      strands.emplace_back(strand_type(io.get_executor()));  
    }  
  
    for (auto idx = 0; idx < num_threads; ++idx) { asio::spawn(strands[idx], crawler(idx)); }  
  
    for (auto idx = 0; idx < num_threads; ++idx) {  
      threads.emplace_back([&io] { io.run(); });  
    }  
  
    for (auto& t : threads) { t.join(); }  
    return 0;  
  }  
```

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:05:59 UTC  
> Url: https://github.com/boostorg/asio/issues/250#issuecomment-752292385  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#700](https://github.com/chriskohlhoff/asio/issues/700).
