# #196 - Logger is unbuffered [Closed]

> Username: mdblack98  
> Created at: 2022-10-02 17:45:25 UTC  
> Updated at: 2022-10-03 13:21:31 UTC  
> Closed at: 2022-10-03 13:21:31 UTC  
> Url: https://github.com/boostorg/log/issues/196  

It seems Logger is unbuffered and I can't seem to find a way to make it buffered.  
It's far too slow unbuffered.  
I have a function that takes less than 2ms to execute but all the logging that occurs with it slows it down to 240ms.  
The same logging done through my own logging function (another program...same library) barely adds 1ms to the total time.  
  
So...  
boostorg/boost#1 Is there an immediate fix to make Logger buffered?  
boostorg/boost#2 I believe Logger should be line-buffered by default as I see many other complaints about it being slow.  If it's logging to a file it should definitely be line buffered.  Logging to console may need unbuffered.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-10-02 20:31:15 UTC  
> Updated at: 2022-10-02 20:32:12 UTC  
> Url: https://github.com/boostorg/log/issues/196#issuecomment-1264727175  

What do you mean by "buffered"?  
  
Sinks forward formatted log records to the standard iostreams, so unless you enable `auto_flush`, they should be using whatever buffering strategy the standard iostreams implement. Additionally, async frontend does not block for the backend to process log records.  
  
Did you profile your code to see where the overhead comes from?

---

## Comment 2

> Username: mdblack98  
> Created at: 2022-10-03 13:09:42 UTC  
> Url: https://github.com/boostorg/log/issues/196#issuecomment-1265415306  

It seems some systems have turned off buffering for stderr in particular. I just fixed this in Hamlib under msys2 with setvbuf which sped up stderr output dramatically . So if buffering is not set deliberately you get different results on different systems.Sent from my iPhoneOn Oct 2, 2022, at 3:31 PM, Andrey Semashev ***@***.***> wrote:﻿  
What do you mean by "buffered"?  
Sinks forward formatted log records to the standard iostreams, so unless you enable auto_flush, they should be using whatever buffering strategy the standard iostreams implement.  
Did you profile your code to see where the overhead comes from?  
  
—Reply to this email directly, view it on GitHub, or unsubscribe.You are receiving this because you authored the thread.Message ID: ***@***.***>

---

## Comment 3

> Username: Lastique  
> Created at: 2022-10-03 13:21:31 UTC  
> Url: https://github.com/boostorg/log/issues/196#issuecomment-1265432021  

Having `stderr` unbuffered might be a reasonable tradeoff for better reliability. In any case, this tradeoff is made by the standard library, and apparently is customizable by user.
