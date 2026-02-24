# #161 - Broken pipe [Closed]

> Username: Mecanik  
> Created at: 2023-06-08 17:37:19 UTC  
> Updated at: 2023-06-11 15:18:17 UTC  
> Closed at: 2023-06-11 15:18:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/161  

Hello,  
  
This is a very common issue I had with my previous (own) implementation of MySQL connector, as well as with the "official" ones... if there is no activity for a while, the connection become stale or something.  
  
In this scenario, after a period of inactivity (a few hours), I get the following:  
  
`Error: [Broken pipe [system:32]], Server diagnostics: []`  
  
Do you have any idea how I can resolve this?  
  
Many thanks!

---

## Comment 1

> Username: Mecanik  
> Created at: 2023-06-08 17:39:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/161#issuecomment-1583077446  

I forgot to add, the latter is:  
  
```  
Error: [Connection reset by peer [system:104]], Server diagnostics: []  
Error: [Broken pipe [system:32]], Server diagnostics: []  
```

---

## Comment 2

> Username: anarthal  
> Created at: 2023-06-09 12:03:53 UTC  
> Url: https://github.com/boostorg/mysql/issues/161#issuecomment-1584468288  

Hi @Mecanik,  
  
These errors mean that the underlying TCP connection with the server has been closed, so you're trying to send data on a TCP connection that is no longer valid.  
  
By default, the MySQL server will close any connection that has been iddle for more than 8h. This behavior is configurable using the [`wait_timeout`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_wait_timeout) global and session variables.  
  
Even if you increase the iddle timeout, the underlying connection may be broken if you keep it iddle for a very long time - IIRC TCP uses keepalive messages to prevent this from happening, but not all systems implement these.  
  
If you need to keep the connection open indefinitely, the most robust strategy is to use application-level messages after a certain period of inactivity is elapsed. In the case of MySQL, I suggest to use [`connection::(async_)ping`](https://www.boost.org/doc/libs/1_82_0/libs/mysql/doc/html/mysql/ref/boost__mysql__connection/ping.html), which is a very lightweight message that verifies that the connection is OK.  
  
Note that `connection` won't send ping messages by default - you will have to implement this yourself. I wanted to keep `connection` as "passive" as possible - it won't engage in any network activity unless you tell it to do so.  
  
If auto-pinging to prevent connections from closing due to inactivity is something of interest, I can try to incorporate it to connection pools when I implement them (see #19).  
  
If you have any further questions, please feel free to ask.  
  
Regards,  
Ruben.

---

## Comment 3

> Username: Mecanik  
> Created at: 2023-06-09 12:08:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/161#issuecomment-1584472893  

Thank you, I understand the issue. The "ping" solution would work I believe (I was thinking to do something like `SELECT now();` or something similar to maintain it).  
  
Please introduce this if possible and if your time allows it. BTW - Why don't you setup some sponsorship?

---

## Comment 4

> Username: Mecanik  
> Created at: 2023-06-09 12:08:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/161#issuecomment-1584473561  

Also, not sure if it makes any difference for your, but I only use MariaDB as MySQL is... well just MySQL.

---

## Comment 5

> Username: anarthal  
> Created at: 2023-06-09 13:36:20 UTC  
> Url: https://github.com/boostorg/mysql/issues/161#issuecomment-1584589245  

`ping()` should be more efficient. All tests are run under MariaDB too, so `ping()` should work correctly in all systems.  
  
I'm currently being sponsored by the C++alliance btw.

---

## Comment 6

> Username: Mecanik  
> Created at: 2023-06-09 14:55:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/161#issuecomment-1584719473  

I see, I need to figure out how to use it. Sorry, C style developer here just dipping my toes into C++ recently. I did not know the C++ alliance has sponsorship; I have a good friend there sehe.

---

## Comment 7

> Username: anarthal  
> Created at: 2023-06-09 15:06:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/161#issuecomment-1584736979  

No worries :)  
  
It is equivalent to [`mysql_ping`](https://mariadb.com/kb/en/mysql_ping/) in the official drivers.

---

## Comment 8

> Username: anarthal  
> Created at: 2023-06-11 15:18:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/161#issuecomment-1586211575  

If you're okay with it, I'm closing this issue and making a note in #19.
