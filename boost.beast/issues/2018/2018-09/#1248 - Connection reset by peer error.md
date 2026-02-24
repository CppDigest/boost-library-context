# #1248 - Connection reset by peer error [Closed]

> Username: bcc791  
> Created at: 2018-09-18 12:33:52 UTC  
> Updated at: 2018-11-26 01:45:48 UTC  
> Closed at: 2018-11-26 01:45:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1248  

I am using the latest version of the boost::beast library (i.e version: 1.68)  
  
I was trying the `sync websocket ` client with ssl (very similar to the provided example) and after many hours of correctly receiving data from the server my code terminated with the following error:  
  
```  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::system::system_error> >'  
what(): Connection reset by peer  
```  
What is  causing this error? If this is unavoidable, should I catch the exception in my read loop and reconnect if it happens?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-09-18 14:31:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1248#issuecomment-422417338  

It means that the remote peer forcibly closed the connection:  
https://stackoverflow.com/questions/1434451/what-does-connection-reset-by-peer-mean  
  
There is nothing you can do about it, and you should either catch the exception or handle the error by using the interfaces which have `error_code&` parameters.

---

## Comment 2

> Username: bcc791  
> Created at: 2018-09-19 22:25:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1248#issuecomment-422978856  

Thanks. I also get the error : "stream truncated" during data reception. (not only during shutdown as reported in issue #824)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-09-20 00:44:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1248#issuecomment-423002777  

That could mean that the remote host closed the connection non-gracefully.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-11-19 01:42:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1248#issuecomment-439747435  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-11-26 01:45:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1248#issuecomment-441495383  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
