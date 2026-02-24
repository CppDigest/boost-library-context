# #1983 - 此库默认的https验证机制 [Closed]

> Username: Yinpinghua  
> Created at: 2020-06-12 03:14:56 UTC  
> Updated at: 2020-06-21 18:10:37 UTC  
> Closed at: 2020-06-21 18:10:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1983  

麻烦问下，此库默认的https验证机制 是什么？

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-06-12 08:07:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1983#issuecomment-643136193  

“ Boost.Beast”将Asio用作其数据传输机制。 Asio可以与许多SSL库一起使用（您需要检查Asio的文档）。验证机制由SSL库确定。

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-06-21 18:10:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1983#issuecomment-647162015  

Question answered. No futher communication. Closing issue.
