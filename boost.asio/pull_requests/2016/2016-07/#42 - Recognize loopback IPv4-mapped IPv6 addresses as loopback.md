# #42 Recognize loopback IPv4-mapped IPv6 addresses as loopback [Closed]

> Username: eric135  
> Created at: 2016-07-22 17:26:38 UTC  
> Updated at: 2017-12-02 07:40:32 UTC  
> Closed at: 2017-12-02 07:40:32 UTC  
> Url: https://github.com/boostorg/asio/pull/42  

Per https://tools.ietf.org/html/rfc4291#section-2.5.5.2, IPv4 addresses can be mapped to IPv6 addresses (`127.0.0.1` turns into `[::ffff:127.0.0.1]`). Since `127.0.0.0/8` addresses are loopback, I would assume that their IPv6 mappings would be loopback as well.  
  
This issue has been previously reported on the Boost bug tracker (https://svn.boost.org/trac/boost/ticket/9084). I believe I've coded a solution to it.

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:40:32 UTC  
> Url: https://github.com/boostorg/asio/pull/42#issuecomment-348675171  

I put this proposed change before the C++ committee (LEWG) in the context of the Networking TS. They preferred the status quo.

---
