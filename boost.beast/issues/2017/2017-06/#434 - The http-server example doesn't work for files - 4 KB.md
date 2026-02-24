# #434 - The http-server example doesn't work for files > 4 KB [Closed]

> Username: niklas88  
> Created at: 2017-06-08 08:12:17 UTC  
> Updated at: 2017-06-09 22:07:47 UTC  
> Closed at: 2017-06-09 22:07:47 UTC  
> Url: https://github.com/boostorg/beast/issues/434  

On master when serving files larger than 4 KB the http-server example only sends the first 4 KB of a file leaving the stream open but not sending any more.  
  
The problem is an inversion of the check for more data in file_buffer. I'll have a Pull Request ready in a bit.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-08 19:45:58 UTC  
> Url: https://github.com/boostorg/beast/issues/434#issuecomment-307207698  

Nice find!
