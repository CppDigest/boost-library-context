# #264 - boost::interprocess::message_queue  Process finished with exit code -1073740791 (0xC0000409) [Closed]

> Username: LetAmericaGreatAgain  
> Created at: 2025-06-05 08:14:07 UTC  
> Updated at: 2026-01-07 08:41:28 UTC  
> Closed at: 2026-01-07 08:41:28 UTC  
> Url: https://github.com/boostorg/interprocess/issues/264  

I used boost::interprocess::message_queue to implement a sender and receiver for interprocess communication. When the sender is used directly in the test program, data is sent and received correctly — the receiver can receive the data without issues.  
  
However, when I build the sender code as a DLL and use it in the test program, sending data causes a problem. The first message is sent successfully, but when sending the second message, the program crashes with the following error:  
  
  
Process finished with exit code -1073740791 (0xC0000409)

---

## Comment 1

> Username: igaztanaga  
> Created at: 2026-01-07 08:41:28 UTC  
> Url: https://github.com/boostorg/interprocess/issues/264#issuecomment-3717849563  

I'm afraid there is no enough information to debug this report. Please re-submit it with a reproducible example so that it can be analyzed.
