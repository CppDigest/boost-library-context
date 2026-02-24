# #220 Possibility to process data between last delimiter and eof while read_until. [Open]

> Username: avvolodin  
> Created at: 2019-04-10 08:42:32 UTC  
> Updated at: 2019-04-10 08:42:32 UTC  
> Url: https://github.com/boostorg/asio/pull/220  

Problem:  
If we have some stream of incoming data with structure  
<data1><delimeter><data2><delimeter><data3><delimeter><data4><eof>  
It's very convinient to use read_until to process such stream. But in current implementation is imposible to process <data4>, becouse in handler value of "bytes_transferred" is always zero when it meet <eof>.  
Solution:  
With this changes, in case of <eof>, "bytes_transferred" remains unchanged to give posibility to process remains data in handler.

---
