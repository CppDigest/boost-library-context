# #74 - Suggest upgrade to RESP3 as possible solution to error [Closed]

> Username: mzimbres  
> Created at: 2023-02-26 16:54:55 UTC  
> Updated at: 2023-02-26 20:00:54 UTC  
> Closed at: 2023-02-26 20:00:53 UTC  
> Url: https://github.com/boostorg/redis/issues/74  

It is easy to forget to upgrade to resp3 before starting sending commands. This leads to error messages that are not very informative, like the one discussed in this issue https://github.com/boostorg/redis/issues/73.  To avoid that, the library could improve the error message from  
  
> Can't convert string to number.  
  
to  
  
> Can't convert string to number. (forgot to upgrade to RESP3?)

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-02-26 20:00:53 UTC  
> Url: https://github.com/boostorg/redis/issues/74#issuecomment-1445453182  

Fix: https://github.com/boostorg/redis/commit/d01a9acf3b5b8f6a942cc8339f2308e9b85889e5
