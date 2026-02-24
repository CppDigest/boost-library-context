# #23 - How to exit gracefully in synchronous mode [Closed]

> Username: wcy168  
> Created at: 2022-08-22 04:34:23 UTC  
> Updated at: 2022-08-23 01:14:30 UTC  
> Closed at: 2022-08-23 01:14:30 UTC  
> Url: https://github.com/boostorg/redis/issues/23  

How to exit gracefully in synchronous mode

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-08-22 19:13:39 UTC  
> Url: https://github.com/boostorg/redis/issues/23#issuecomment-1222802973  

The correct way is to  
  
1. Set `connection::config::enable_reconnect = false` (if not already set)  
2. Send a `QUIT` command.  
  
This will cause `sync::run()` to return. Otherwise, you may have an `io_context::run()` that is not returning, in this case you have to stop giving him further work, in extreme cases you can also call `io_context::stop()`. For an example see https://github.com/mzimbres/aedis/blob/master/examples/intro_sync.cpp.  
  
Notice Aedis 1.0.0 was release yesterday and all synchronous functions are now part of the `sync` class. There were also a fixe of a bug that prevented proper reconnection. See the changelog for more info https://mzimbres.github.io/aedis/md_CHANGELOG.html

---

## Comment 2

> Username: wcy168  
> Created at: 2022-08-23 01:14:28 UTC  
> Url: https://github.com/boostorg/redis/issues/23#issuecomment-1223397043  

ok，thank you
