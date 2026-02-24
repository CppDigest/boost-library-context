# #2061 - 关于asio如何限制linux虚拟ip的bind [Closed]

> Username: yph025869  
> Created at: 2020-08-25 03:49:51 UTC  
> Updated at: 2020-10-12 00:33:06 UTC  
> Closed at: 2020-10-12 00:33:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2061  

我的机器有个网卡，还有一个虚拟ip,我想auto end_point = tcp::endpoint(tcp::v4(), enp.port()); 使用这行代码的时候，限制虚拟ip的绑定，麻烦问下，我代码该怎么设置

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-25 06:29:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2061#issuecomment-679830380  

我不清楚你在问什么。  
是否要绑定到计算机上的所有地址？ 还是要绑定到特定的地址和端口？

---

## Comment 2

> Username: yph025869  
> Created at: 2020-08-25 08:01:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2061#issuecomment-679869891  

绑定，除了特定的地址

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-08-28 06:15:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2061#issuecomment-682347360  

https://www.boost.org/doc/libs/1_74_0/doc/html/boost_asio/reference/ip__address_v4/any.html  
  
您可以使用它作为地址

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-10-04 03:01:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2061#issuecomment-703193594  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2020-10-12 00:32:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2061#issuecomment-706795753  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
