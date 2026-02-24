# #54 Non-blocking adapter write() consistent with read(). [Closed]

> Username: msuvajac  
> Created at: 2018-03-02 10:53:24 UTC  
> Updated at: 2018-04-17 05:30:07 UTC  
> Closed at: 2018-04-17 05:30:07 UTC  
> Url: https://github.com/boostorg/iostreams/pull/54  

Same behavior for when nothing was written as read().

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-04-15 03:11:00 UTC  
> Url: https://github.com/boostorg/iostreams/pull/54#issuecomment-381376837  

I do not like this change. The iostreams documentation explains 'write' as returning the number of charcacters written while 'read' is explained as returning the number of characters read or -1 on EOF. So I do not think we should be changing 'write' as you have done so in your PR.

---

## Comment 2

> Username: msuvajac  
> Created_at: 2018-04-17 05:30:07 UTC  
> Url: https://github.com/boostorg/iostreams/pull/54#issuecomment-381848179  

I agree. Closing pull request.  
Also, I've just noticed that by that logic if n == 0 (0 bytes requested to read() or write()) you'll end up with an infinite loop in this adapter.

---
