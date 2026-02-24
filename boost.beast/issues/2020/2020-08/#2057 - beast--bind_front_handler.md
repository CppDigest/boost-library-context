# #2057 - beast::bind_front_handler [Closed]

> Username: Yph0258691  
> Created at: 2020-08-20 10:22:39 UTC  
> Updated at: 2020-10-04 03:01:49 UTC  
> Closed at: 2020-10-04 03:01:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2057  

麻烦问下，这个函数内部实现是不是原子操作的？也就是asio底层投递的消息，被asio只触发一次？

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-20 10:29:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2057#issuecomment-677515363  

是的，当调用此函数返回的处理程序时，它将立即调用添加了参数的绑定处理程序。 调用不会发布到关联的执行者。

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-08-20 13:29:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2057#issuecomment-677665356  

我可以解决这个问题吗？

---

## Comment 3

> Username: Yph0258691  
> Created at: 2020-08-21 01:10:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2057#issuecomment-677982826  

谢谢

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-09-20 03:28:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2057#issuecomment-695613585  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2020-10-04 03:01:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2057#issuecomment-703193620  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
