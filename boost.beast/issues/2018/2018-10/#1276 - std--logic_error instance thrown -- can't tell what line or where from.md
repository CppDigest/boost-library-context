# #1276 - std::logic_error instance thrown -- can't tell what line or where from [Closed]

> Username: DragonOsman  
> Created at: 2018-10-24 18:03:36 UTC  
> Updated at: 2018-11-28 02:09:33 UTC  
> Closed at: 2018-11-28 02:09:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1276  

When I tried to run my app from my Ubuntu VM after re-building it there, it crashed with the following error message:  
```  
terminate called after throwing an instance of 'std::logic_error'  
  what():  basic_string::_M_construct null not valid  
Aborted (core dumped)  
```  
I've added line numbers to my code to document where errors came from, but this doesn't show line numbers.  So it can't be from my own code.  How do I find out if it's from Beast or from Asio, and what line of code it's from?  And how do I fix the error?  
  
Just in case, I'll also give a link to my own code here: https://gist.github.com/DragonOsman/d00ea7ec3d49c128ad7789293f156a09 .  Any help is appreciated.  Thanks in advance.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-10-24 18:16:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1276#issuecomment-432770994  

See: https://markheath.net/post/effective-debugging-with-divide-and-conquer

---

## Comment 2

> Username: DragonOsman  
> Created at: 2018-10-24 21:51:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1276#issuecomment-432841134  

I'll try running it through GDB on CodeBlocks (though when I last tried it on the command line, the program wasn't running when I tried to set a breakpoint and step through the code).

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-11-27 01:09:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1276#issuecomment-441859423  

This issue has been open for a while with no activity, has it been resolved?
