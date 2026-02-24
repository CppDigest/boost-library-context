# #574 - Read entire message in http-server-fast [Closed]

> Username: vinniefalco  
> Created at: 2017-07-02 15:36:32 UTC  
> Updated at: 2017-08-16 00:26:45 UTC  
> Closed at: 2017-08-16 00:26:45 UTC  
> Url: https://github.com/boostorg/beast/issues/574  

http-server-fast would be even faster if it didn't split the read. @chriskohlhoff what do you think?

---

## Comment 1

> Username: chriskohlhoff  
> Created at: 2017-07-03 10:46:59 UTC  
> Url: https://github.com/boostorg/beast/issues/574#issuecomment-312613669  

Seems reasonable, so I updated my copy with this change (and some other tweaks) here:  
  
https://gist.github.com/chriskohlhoff/b0ba99a54c9ca6fcd5918592902fd434

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-03 12:57:51 UTC  
> Url: https://github.com/boostorg/beast/issues/574#issuecomment-312638814  

Would you mind if I attach your GitHub id to the commit? Since I am just taking everything you wrote and updating Beast sources with it?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-08-16 00:26:45 UTC  
> Url: https://github.com/boostorg/beast/issues/574#issuecomment-322624941  

Done
