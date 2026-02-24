# #2040 - how to close websocket from another thread safely [Closed]

> Username: tesla1060  
> Created at: 2020-07-28 14:28:13 UTC  
> Updated at: 2020-09-05 19:10:10 UTC  
> Closed at: 2020-09-05 19:10:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2040  

Hi,  
my websocket is defined as below,  
  
`websocket::stream<ssl::stream<tcp::socket>> ws;`  
  
I have two threads, one has a loop with the websocket `read`,  and send out `ping` periodically,  
  
```  
while(true):  
    ws.read(buffer, ec);  
```  
  
and the second thread that periodically check `pong`, if `pong` is timeout, the second thread will initiate `ws.close(websocket::close_code::normal)`  
  
The problem is sometimes this assertion is trigger in `boost/beast/websocket/impl/read.hpp:1045`  
  
`BOOST_ASSERT(! impl.rd_close);`  
  
This seems to be a problem of closing `ws` from another thread. any suggestion how to close from another thread?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-07-28 14:31:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2040#issuecomment-665075537  

Use the asynchronous APIs and make sure that you only access the websocket stream from one thread.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2020-08-29 10:13:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2040#issuecomment-683269356  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2020-09-05 19:10:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2040#issuecomment-687650046  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
