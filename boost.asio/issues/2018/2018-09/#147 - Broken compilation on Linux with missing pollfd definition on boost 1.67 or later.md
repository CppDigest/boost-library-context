# #147 - Broken compilation on Linux with missing pollfd definition on boost 1.67 or later [Closed]

> Username: grossag  
> Created at: 2018-09-20 18:54:41 UTC  
> Updated at: 2020-12-30 00:53:16 UTC  
> Closed at: 2020-12-30 00:53:15 UTC  
> Url: https://github.com/boostorg/asio/issues/147  

When upgrading code that consumes boost to boost 1.67 or later, Linux builds are failing because they can't find a pollfd definition. I believe that this is due to https://github.com/boostorg/asio/commit/84572fb9be75b31a5aea8444aea0a37b88de7f74#diff-158ced0350d9cc27c363330aad0f700b . Specifically, various Linux builds are no longer able to get the definition of pollfd because <poll.h> doesn’t give it to them (although <sys/poll.h> does). It appears that the boost asio codebase in several places assumes that it will get a pollfd definition from socket_types.hpp but they no longer get it from there after the aforementioned commit.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:53:14 UTC  
> Url: https://github.com/boostorg/asio/issues/147#issuecomment-752289913  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#639](https://github.com/chriskohlhoff/asio/issues/639).
