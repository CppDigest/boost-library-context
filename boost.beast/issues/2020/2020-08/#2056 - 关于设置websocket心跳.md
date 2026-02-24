# #2056 - 关于设置websocket心跳 [Closed]

> Username: Yph0258691  
> Created at: 2020-08-19 09:11:02 UTC  
> Updated at: 2020-10-04 03:01:47 UTC  
> Closed at: 2020-10-04 03:01:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2056  

ws_http_.set_option(  
		websocket::stream_base::timeout::suggested(  
			beast::role_type::server))  
麻烦问下，是不是使用这个函数，就可以实现心跳检测了

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-19 14:07:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2056#issuecomment-676416349  

是的，当担任服务器角色时，WebSocket将自动启动心跳。 担任客户端角色时，要启动心跳，您必须设置超时值或自己执行心跳。

---

## Comment 2

> Username: Yph0258691  
> Created at: 2020-08-20 05:13:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2056#issuecomment-677096503  

好谢谢啊

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-08-20 13:30:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2056#issuecomment-677665527  

我可以解决这个问题吗？

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-09-20 03:28:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2056#issuecomment-695613632  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2020-10-04 03:01:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2056#issuecomment-703193617  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
