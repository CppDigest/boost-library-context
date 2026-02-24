# #2098 - 关于beast库https如何跳转http [Closed]

> Username: Yinpinghua  
> Created at: 2020-09-22 05:10:29 UTC  
> Updated at: 2020-11-16 03:45:59 UTC  
> Closed at: 2020-11-16 03:45:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2098  

麻烦问下，https 如何跳转到http(使用302跳转)

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-09-22 05:30:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2098#issuecomment-696515803  

要进行重定向，您需要检查新位置字段是否包含新方案FQDN，以及它是相对路径还是绝对路径。 如果它包含新的FQDN或方案，则需要建立新的连接才能继续请求链。

---

## Comment 2

> Username: stale[bot]  
> Created at: 2020-11-01 07:41:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2098#issuecomment-720048917  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2020-11-16 03:45:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2098#issuecomment-727717752  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
