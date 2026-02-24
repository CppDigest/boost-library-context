# #295 - How to use one connection for several requests? [Closed]

> Username: AndreyAlifanov  
> Created at: 2025-08-28 12:46:20 UTC  
> Updated at: 2025-08-29 06:13:00 UTC  
> Closed at: 2025-08-29 06:12:59 UTC  
> Url: https://github.com/boostorg/redis/issues/295  

Hello.  
  
I need to know: how to use connection to redis for several requests?  
Now I'm able to do only one request per connection.  
  
If I try to send second request per connection - code hangs.  
For next request I need to create new connection.  
  
I use C++17 standard, so I adopted example from documentation for sync_connection.  
  
My code looks like this:  
  
```C++  
Response DoCommand(...)  
{  
    // I want to do these 2 lines only once (maybe to reconnect in fail case, but not for every command)  
	redis::sync_connection conn;  
	conn.run(ConfigureConnection());  
  
	boost::redis::request req;  
	req.push(...);  
  
    // here all is ok  
	boost::redis::response<...> resp;  
	conn.exec(req, resp);  
	conn.stop();  
  
    // here we hang  
	boost::redis::request req2;  
	boost::redis::response<...> resp2;  
	req2.push("PING");  
	conn.exec(req2, resp2);  <--  here  
	conn.stop();  
  
	return HandleResponse(resp);  
}  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2025-08-28 13:33:15 UTC  
> Url: https://github.com/boostorg/redis/issues/295#issuecomment-3233527143  

You should only call stop() once, after you've run all your requests. stop() will kill the connection's internal task, which coordinates request handling. After calling stop(), requests will block until run() is called again.

---

## Comment 2

> Username: AndreyAlifanov  
> Created at: 2025-08-29 06:13:00 UTC  
> Url: https://github.com/boostorg/redis/issues/295#issuecomment-3235849179  

Hi, @anarthal   
  
It was not correct artificial sample, and in reality I use different code.  
But with information from you I've got result, I expected.  
  
Thank you very much!!!
