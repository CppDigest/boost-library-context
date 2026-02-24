# #31 - Missing example [Closed]

> Username: heretic13  
> Created at: 2016-10-12 19:12:32 UTC  
> Updated at: 2016-10-12 19:31:30 UTC  
> Closed at: 2016-10-12 19:31:30 UTC  
> Url: https://github.com/boostorg/log/issues/31  

Example folder cannot contain example with udp_socket_based for syslog_backend.

---

## Comment 1

> Username: heretic13  
> Created at: 2016-10-12 19:22:09 UTC  
> Updated at: 2016-10-12 19:27:42 UTC  
> Url: https://github.com/boostorg/log/issues/31#issuecomment-253311969  

No example & no test - > not caught bugs  
https://github.com/boostorg/log/issues/32

---

## Comment 2

> Username: Lastique  
> Created at: 2016-10-12 19:31:30 UTC  
> Url: https://github.com/boostorg/log/issues/31#issuecomment-253314354  

I'm not sure what you're saying but there are syslog and native_syslog examples. Converting either of them to UDP-based is trivial, I don't think there's a need for a separate example for that.  
  
Regarding tests, Boost testing infrastructure is not really tailored for these kind of tests, hence no tests.
