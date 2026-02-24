# #2171 - 关于使用multipart/form-data请求 [Closed]

> Username: Yinpinghua  
> Created at: 2021-02-24 10:13:28 UTC  
> Updated at: 2021-12-29 16:30:58 UTC  
> Closed at: 2021-03-01 01:20:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2171  

能不能给个multipart/form-data 请求的代码列子

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-02-24 10:38:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2171#issuecomment-784981929  

客户端代码作者负责解码HTTP BODY中的数据。  
野兽没有设施来帮助这一点。  
我们认为FORM DATA是“应用程序的关注点”

---

## Comment 2

> Username: Yinpinghua  
> Created at: 2021-02-24 11:02:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2171#issuecomment-784995972  

我自己搞定了，谢谢啊

---

## Comment 3

> Username: Yinpinghua  
> Created at: 2021-03-01 01:20:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2171#issuecomment-787572493  

我已解决

---

## Comment 4

> Username: 1s23  
> Created at: 2021-12-29 03:53:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2171#issuecomment-1002384739  

> 我已解决  
  
想问下是怎么解决的，可以给个例子吗，感谢

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-12-29 16:30:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2171#issuecomment-1002676968  

http://www.codesink.org/mimetic_mime_library.html
