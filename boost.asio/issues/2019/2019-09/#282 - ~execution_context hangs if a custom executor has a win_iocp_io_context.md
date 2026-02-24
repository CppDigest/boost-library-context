# #282 - ~execution_context hangs if a custom executor has a win_iocp_io_context [Closed]

> Username: Marc-Aldorasi-Imprivata  
> Created at: 2019-09-10 18:27:48 UTC  
> Updated at: 2020-12-30 01:09:22 UTC  
> Closed at: 2020-12-30 01:09:21 UTC  
> Url: https://github.com/boostorg/asio/issues/282  

The [attached program](https://github.com/boostorg/asio/files/3597161/hang.txt) hangs when run on Windows.  This is because socket's constructor creates a win_iocp_io_context which has its own thread.  When the context is destroyed it joins on the win_iocp_io_context's thread, but the thread is waiting on a completion port.  Since nothing has been posted to the port, the thread waits forever, causing the join to hang forever.  This can be worked around by calling stop immediately before joining, but I'm not sure how that behaves if any work is actually posted to that thread.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:09:20 UTC  
> Url: https://github.com/boostorg/asio/issues/282#issuecomment-752292915  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#718](https://github.com/chriskohlhoff/asio/issues/718).
