# #62 - COM_PING support [Closed]

> Username: anarthal  
> Created at: 2022-05-11 11:33:32 UTC  
> Updated at: 2023-02-11 12:02:42 UTC  
> Closed at: 2023-02-11 12:02:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/62  

Support the PING (https://dev.mysql.com/doc/dev/mysql-server/latest/page_protocol_com_ping.html) protocol mechanism.

---

## Comment 1

> Username: siladic  
> Created at: 2022-10-06 06:43:36 UTC  
> Url: https://github.com/boostorg/mysql/issues/62#issuecomment-1269423749  

As you are probably aware, PING is vital for detecting half-open TCP connection. Without PING, it is impossible to detect broken connection in a reliable manner. Timeouts on async_* commands would not help since it is impossible to estimate correctly how long a query would last. Consequently, it is impossible to reliably detect circumstances for reconnect.  
  
Now, a quick question just to point us to the right direction: since COM_PING is effectively "pipelined" (multiplexed) with other commands, how difficult would be to distinguish OK_Packet as reply to COM_PING vs reply to COM_QUERY in boost.mysql code? Or it would be better to wait for generic pipelining support described in #75?

---

## Comment 2

> Username: anarthal  
> Created at: 2022-10-06 12:32:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/62#issuecomment-1269956933  

How are you currently handling the pipelining?  
  
As far as I can tell, right now there is no way to reliably pipeline a COM_PING and a COM_QUERY in the same command, as `connection::query` writes the COM_QUERY and immediately reads the response. If you manually write the COM_PING packet and then call `connection::query`, the read operation for query will mistakenly read the response to COM_PING as if it were COM_QUERY's response.  
  
What's the use case for this pipelining, though? Are you sending a COM_PING immediately followed by a COM_QUERY? What's the advantage of this over just issuing the COM_QUERY and letting it fail if the connection is broken?

---

## Comment 3

> Username: siladic  
> Created at: 2022-10-06 13:01:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/62#issuecomment-1270002726  

Our goal is not the query pipelining itself, nor we implement it in any way right now. Our goal is to detect half open TCP connection and close the socket / reconnect if necessary. The simplest way to provoke such scenario is to run a query from a client machine that would last for, say, 10 seconds, and then while the query is running on the server, simply unplug the ethernet cable from the client machine. The client code will see its socket as "alive" until a very long time or never (if TCP keep alive is set, the kernel will eventually detect it as "dead", but TCP keep alive is set to no less than 2 hours by default). To make things worse, you cannot unilaterally set TCP keep alive timeout on a client because intermediate routers may change it at a will.  
  
PING resolves the above problem because server can reply to PING message immediately and hence the PING roundtrip is relatively predictable. If there's no PONG (i.e. OK_Packet in MySQL language) within reasonably short time, you consider the connection to be dead, shutdown the socket and connect again.  
  
The same concept is built into the MQTT protocol for exactly the same purpose (https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901195).  
  
However, as I was afraid, you have explained very precisely that you cannot distinguish between replies to COM_PING and COM_QUERY even if you would implement pipelining in the sense which you have described in #75.  
  
To sum up, it is conceptually not possible to implement PING in a MQTT-ish manner with the MySQL protocol. Anyway, thanks for your comments!

---

## Comment 4

> Username: anarthal  
> Created at: 2022-10-06 17:48:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/62#issuecomment-1270465644  

AFAIK the MySQL server processes requests in a strictly sequential order. If you send the 10sec lasting query and then immediately the COM_PING request (in a pipelined way), the server won't process the COM_PING until it ends processing the previous COM_QUERY command. So you would get the PONG response right after your query results. If your query took 20sec to run, your PONG response will take 20sec to arrive.  
  
I haven't tried this, but there isn't anything at the protocol level to distinguish between messages other than order (responses are received in the same order as commands are sent). In this sense, the protocol is like HTTP.  
  
Having a look at libmysqlclient, they don't actively use PING as you described. It's just exposed and left to the user to use it or not. I don't think there is an equivalent to MQTT's PING.  
  
For any further questions, please let me know.  
  
Cheers,  
Ruben.

---

## Comment 5

> Username: siladic  
> Created at: 2022-10-06 18:59:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/62#issuecomment-1270550574  

Yep, it seems MySQL server indeed processes PING sequentially exactly the way that you've described. I think there's no other way to detect half-open connection but to open another connection to the same server and us it exclusively for health checking. In that case one can use even more complex health checks than plain PING, something along the line what people are doing with a various HAProxy mysql checks.  
  
Thanks, and wish you that boost includes this library soon!  
Ivica
