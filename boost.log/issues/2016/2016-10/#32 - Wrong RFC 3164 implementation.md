# #32 - Wrong RFC 3164 implementation [Closed]

> Username: heretic13  
> Created at: 2016-10-12 19:27:16 UTC  
> Updated at: 2016-10-16 12:48:57 UTC  
> Closed at: 2016-10-16 12:48:57 UTC  
> Url: https://github.com/boostorg/log/issues/32  

Hello.  
  
RFC 3164 implementation for udp_socket_based for syslog_backend broken.  
  
Modified example https://github.com/boostorg/log/blob/develop/example/native_syslog/main.cpp  
generate message:  
`<190> Oct  12 21:58:45 DESKTOP-G84C4JR native_syslog: 0: A syslog record with normal level`  
2 spaces between Oct & 12 (many thanks to github bugged editor)  
  
1st:  
`The TIMESTAMP will immediately follow the trailing ">" from the PRI part`  
  
2nd:  
`The TIMESTAMP field is the local time and is in the format of "Mmm dd hh:mm:ss"`  
  
maybe another bugs, but I don't check

---

## Comment 1

> Username: heretic13  
> Created at: 2016-10-13 06:52:00 UTC  
> Url: https://github.com/boostorg/log/issues/32#issuecomment-253429121  

In function syslog_udp_socket::send_message used wrong format string:  
  
"<%d> %s % 2d %02d:%02d:%02d %s %s"

---

## Comment 2

> Username: Lastique  
> Created at: 2016-10-16 12:48:57 UTC  
> Url: https://github.com/boostorg/log/issues/32#issuecomment-254045058  

As far as I can see, the only incorrect part is that space between the PRI field and month. The date/time itself is correct. I removed the space in 084f848d1461f16c0a6871694076aa36995d4f80.
