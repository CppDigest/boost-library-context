# #15 Throw error on fcntl fail [Closed]

> Username: tempoz  
> Created at: 2015-02-25 20:13:21 UTC  
> Updated at: 2021-05-06 20:51:45 UTC  
> Closed at: 2021-05-06 20:51:45 UTC  
> Url: https://github.com/boostorg/asio/pull/15  

If fcntl fails, it ought not fail silently. This issue is related to CID14947, CID14946, CID14949, and CID14948 in Coverity, and was uncovered by Coverity.

---
