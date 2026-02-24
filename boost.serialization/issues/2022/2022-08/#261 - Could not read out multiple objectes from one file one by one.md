# #261 - Could not read out multiple objectes from one file one by one [Open]

> Username: NotOneRing  
> Created at: 2022-08-30 06:50:49 UTC  
> Updated at: 2022-09-15 21:59:20 UTC  
> Url: https://github.com/boostorg/serialization/issues/261  

Dear authors,  
  
Sorry for the inconvenience I might cause.  
  
I'm trying to serialize multiple objects into one file and read them out into multiple objects in C++. Although I could write them one by one, the file could not be read out one by one into multiple objects. Instead, the file is read out into one object, and all the following objects would be empty.   
  
Is there anything I missed? Could you please help me out?  
  
Best regards,  
David

---

## Comment 1

> Username: robertramey  
> Created at: 2022-09-15 21:59:20 UTC  
> Url: https://github.com/boostorg/serialization/issues/261#issuecomment-1248676810  

There are numerous examples/test of writing out/reading back multiple objects (and multiple object types) into a archive.  I would suggest making a very small test program to illustrate the issue. if this fails and it seems it shouldn't.  send the example to me. If it passes, just keeping making it more elaborate until it fails.  
  
I'm guessing that this is a misunderstanding on how to use the library.  If you've already resolved it, let me know so I can close this issue.  If you're still stuck, try the above and get back to me.
