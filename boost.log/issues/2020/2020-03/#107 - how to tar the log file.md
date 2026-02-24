# #107 - how to tar the log file [Closed]

> Username: mozeat-sun  
> Created at: 2020-03-04 14:08:32 UTC  
> Updated at: 2020-03-04 14:20:58 UTC  
> Closed at: 2020-03-04 14:20:58 UTC  
> Url: https://github.com/boostorg/log/issues/107  

hi  
   tar file is good idea to save memory while sigle file exceed the limit, any example for me to learn ?,thanks.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-03-04 14:20:58 UTC  
> Url: https://github.com/boostorg/log/issues/107#issuecomment-594551170  

I'm not sure what you're asking. Boost.Log does not create tar files. You can create your own sink that does that. Or use [logrotate](https://linux.die.net/man/8/logrotate).
