# #191 - Too many connections err_packet is not deserialized correctly [Closed]

> Username: anarthal  
> Created at: 2023-12-19 15:14:19 UTC  
> Updated at: 2023-12-26 16:04:12 UTC  
> Closed at: 2023-12-26 16:04:12 UTC  
> Url: https://github.com/boostorg/mysql/issues/191  

It looks like MySQL uses a different format for some error packets, omitting some fields. This is the case for the "too many connections" packet. This does not look to trigger UB, but the error message and error code are not correct.
