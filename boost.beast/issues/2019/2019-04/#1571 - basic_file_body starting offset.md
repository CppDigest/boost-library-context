# #1571 - basic_file_body starting offset [Closed]

> Username: mao-circlecvi  
> Created at: 2019-04-13 13:35:55 UTC  
> Updated at: 2019-04-13 13:42:36 UTC  
> Closed at: 2019-04-13 13:42:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1571  

Version: 1.70/master  
  
Hi vinniefalco, first of all, thank you for the wonderful beast library.  
  
Background: I am developing a small web server for serving collections of small files.  
Each collection has tens of thousands small files (10K to 500K in size per file).  
Therefore I bundle the files in a huge volume and each file has a offset/size.  
  
I notice that `basic_file_body` assumes that it sends whole file instead of a given offset/size.  
At least for Windows, the code in `file_body_win32.hpp` handles the `Offset`/`OffsetHigh` fields in `OVERLAPPED` struct pretty well. So I think the support for this mechanism is quite ready.  
  
Does adding support for file_body with a given starting offset/size fit in your overall design/plan?   
  
Thanks!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-13 13:38:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1571#issuecomment-482810069  

Yes I do plan to support serving a range of a file, but the HTTP code is due for an overhaul and I am not ready to make those changes yet. In the meanwhile, you might consider simply creating your own Body type that does what you need.

---

## Comment 2

> Username: mao-circlecvi  
> Created at: 2019-04-13 13:42:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1571#issuecomment-482810360  

Thank you for the reply!  
  
Sure, I will do so.
