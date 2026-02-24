# #2305 - 关于http是否可以先发包头再发包体的思路 ( Extend ) [Closed]

> Username: zhuxiaoguang  
> Created at: 2021-08-31 02:59:46 UTC  
> Updated at: 2021-09-01 12:17:52 UTC  
> Closed at: 2021-09-01 12:17:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2305  

我的想法： 先给客户端发包头，再给客户端多次发包体，麻烦问下，是否可行？  
  
Like this：  
  
     --> send_header ( E.g. :  content-length: { 20MB } )  
     --> wait for data producing ... { maybe wait 1min, size: 1MB }  
     --> send_body  
     --> wait for data producing again { maybe 5min, size: 5MB }  
     --> send_body  
     -->  repeat ...  
  
不知道我描述清楚了没有。

---

## Comment 1

> Username: zhuxiaoguang  
> Created at: 2021-09-01 12:17:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2305#issuecomment-910228907  

Find it.
