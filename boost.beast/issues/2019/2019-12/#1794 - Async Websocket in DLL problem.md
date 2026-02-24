# #1794 - Async Websocket in DLL problem [Closed]

> Username: rraggerr  
> Created at: 2019-12-25 17:05:12 UTC  
> Updated at: 2020-04-24 18:08:32 UTC  
> Closed at: 2020-04-24 18:08:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1794  

Hello, im trying to un-load my DLL which uses async websocket, so im closing the connection and everything is fine, but when im using FreeLibrary , my DLL unloads from my proccess but Beast or websockets "still loaded". When im trying to recompile the DLL, Visual Studio says what something using this file and it cant be overwritten.  
  
You can easily recreate this by compiling this [example](https://github.com/boostorg/beast/blob/develop/example/websocket/client/async/websocket_client_async.cpp) like an dll (just add DllMain thing and FreeLibrary at the end of main thread which you create in DllMain) -> https://pastebin.com/fvwsaa9G  
  
I guess its problem of Boost Asio and Asio ( because i tried to use this [websocket](https://github.com/zaphoyd/websocketpp), but it has same problem)  
Compiler : Visual Studio 2019  
Beast version : 266

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-25 17:06:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1794#issuecomment-568916576  

Is this relevant? https://stackoverflow.com/questions/43904050/c-sharp-using-freelibrary-to-unload-dynamic-loading-of-c-unmanaged-dll-does-no

---

## Comment 2

> Username: rraggerr  
> Created at: 2019-12-25 17:07:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1794#issuecomment-568916626  

> Is this relevant? https://stackoverflow.com/questions/43904050/c-sharp-using-freelibrary-to-unload-dynamic-loading-of-c-unmanaged-dll-does-no  
  
I do not know much about C#, but looks like yes.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-12-25 17:12:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1794#issuecomment-568916980  

Are you using OpenSSL? Is this answer relevant?  
  
> I eventually did find the root cause of this problem. The issue is using the shared dynamically-linked SSL library. Even though I release memory my DLL uses for interface to SSL library, the OS keeps a portion of my DLL locked in memory due to the way the SSL library initializes itself when called. From what I remember with my research, there's no good existing way to correct this other than statically linking the SSL source in my DLL

---

## Comment 4

> Username: rraggerr  
> Created at: 2019-12-25 17:13:52 UTC  
> Updated at: 2019-12-25 17:14:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1794#issuecomment-568917039  

> Are you using OpenSSL? Is this answer relevant?  
>   
> > I eventually did find the root cause of this problem. The issue is using the shared dynamically-linked SSL library. Even though I release memory my DLL uses for interface to SSL library, the OS keeps a portion of my DLL locked in memory due to the way the SSL library initializes itself when called. From what I remember with my research, there's no good existing way to correct this other than statically linking the SSL source in my DLL  
  
No, i dont use OpenSSL, pure Boost Beast

---

## Comment 5

> Username: rraggerr  
> Created at: 2019-12-26 08:34:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1794#issuecomment-569012255  

So i've updated Boost library to 1.72, now Beast version is 277. Problem still exists.

---

## Comment 6

> Username: stale[bot]  
> Created at: 2020-01-25 09:29:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1794#issuecomment-578391190  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: rraggerr  
> Created at: 2020-01-25 14:08:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1794#issuecomment-578409168  

nope

---

## Comment 8

> Username: stale[bot]  
> Created at: 2020-02-24 15:07:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1794#issuecomment-590367885  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: rraggerr  
> Created at: 2020-02-25 03:54:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1794#issuecomment-590670277  

no

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-04-24 18:08:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1794#issuecomment-619166764  

There isn't much we can do here, treatment of DLLs is outside the scope of Beast. Unless there is an actual bug in Beast, we can't do anything about it, so I'll go ahead and close this issue.
