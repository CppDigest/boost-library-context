# #609 - -l option (timeout) to b2 [Closed]

> Username: MakerMatrix  
> Created at: 2020-05-18 21:50:14 UTC  
> Updated at: 2020-05-18 22:46:22 UTC  
> Closed at: 2020-05-18 22:26:21 UTC  
> Url: https://github.com/boostorg/build/issues/609  

I just experienced an issue that caused me to lose half a day, and I want to understand where this needs to be reported.  
  
I will start with a question:  is it common for make to pass its options to b2?  If a user invokes make, is it default that b2 will inherit those commandline options?  Because if it is, I need to report a bug here.  If it isn't I need to report a bug to the application whose build scripts caused my issue.  
  
The issue:  I passed "-l 4" to make, to throttle its scheduler to where load average stays <= NCPUs.  But that got passed to b2, where it caused many of the objects I was building to time out, ultimately causing the link stage to fail.  
  
I don't know whose fault that is.  Trying to understand.  Thanks.  
  
Edit: I mean, it's my fault for using a little-used feature of Make.  But I had a good reason.  I was building on a Pi and without that option, make was running the poor little Pi out of RAM.  (I tried -j2 but a submake was overriding that).

---

## Comment 1

> Username: MakerMatrix  
> Created at: 2020-05-18 22:26:21 UTC  
> Url: https://github.com/boostorg/build/issues/609#issuecomment-630468035  

This may have been a horrendous red herring caused by a gigantic coincidence.  Closing until I learn more.

---

## Comment 2

> Username: MakerMatrix  
> Created at: 2020-05-18 22:46:22 UTC  
> Url: https://github.com/boostorg/build/issues/609#issuecomment-630474317  

PEBKAC
