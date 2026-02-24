# #330 - 100% CPU after waking from sleep (reported from qBT, Windows 10) [Closed]

> Username: rafi-d  
> Created at: 2020-03-08 13:05:46 UTC  
> Updated at: 2020-12-30 01:13:28 UTC  
> Closed at: 2020-12-30 01:13:27 UTC  
> Url: https://github.com/boostorg/asio/issues/330  

https://github.com/arvidn/libtorrent/issues/2977#issuecomment-596195153  
https://github.com/qbittorrent/qBittorrent/issues/7104  
  
Reproduced by  terminating the interruptor socket here:  
https://github.com/arvidn/libtorrent/issues/2977#issuecomment-596195153

---

## Comment 1

> Username: arvidn  
> Created at: 2020-03-08 14:32:58 UTC  
> Url: https://github.com/boostorg/asio/issues/330#issuecomment-596211967  

I think a more accurate title for this ticket is "socket_select_interrupter busy loop when waking up from sleep".  
  
I also don't think this is the right github project to file this ticket against.

---

## Comment 2

> Username: rafi-d  
> Created at: 2020-03-08 14:41:03 UTC  
> Url: https://github.com/boostorg/asio/issues/330#issuecomment-596212808  

Which project is the right one?

---

## Comment 3

> Username: arvidn  
> Created at: 2020-03-08 14:42:04 UTC  
> Url: https://github.com/boostorg/asio/issues/330#issuecomment-596212920  

https://github.com/chriskohlhoff/asio/issues/461

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 01:13:26 UTC  
> Url: https://github.com/boostorg/asio/issues/330#issuecomment-752293494  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#751](https://github.com/chriskohlhoff/asio/issues/751).
