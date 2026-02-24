# #76 - Commit limit should be adaptive [Closed]

> Username: vinniefalco  
> Created at: 2016-09-06 15:21:52 UTC  
> Updated at: 2016-09-09 21:09:15 UTC  
> Closed at: 2016-09-09 21:09:15 UTC  
> Url: https://github.com/boostorg/beast/issues/76  

The commit limit blocks insertions when the sustained throughput of inserts exceeds the throughput limit of the commit process. Currently this limit is set to an arbitrary high value determined empirically. It should be set using an adaptive calculation.
