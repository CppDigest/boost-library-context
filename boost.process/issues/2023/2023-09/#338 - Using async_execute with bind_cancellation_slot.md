# #338 - [v2] Using async_execute with bind_cancellation_slot [Closed]

> Username: Chardrazle  
> Created at: 2023-09-08 07:59:42 UTC  
> Updated at: 2023-10-05 11:07:58 UTC  
> Closed at: 2023-10-04 22:52:21 UTC  
> Url: https://github.com/boostorg/process/issues/338  

I've been cutting over some _process_ v1 code to v2, and (due to the asynchronous nature of the use-case) went via the `async_execute` route (and _asio_).  
  
It's all good except for the `cancellation_signal`, insofar as `total` and `partial` have no effect on the child process.    
The code has been written in a similar manner to the example, with a (timer) fallback through `total`/`partial` and then `terminate`, with the latter successfully pulling the plug.    
However, this isn't ideal as I really need the _SIGINT_ detection for clean up (in the child process).  
  
The child process in this case is a _bash_ script, with _trap_ handlers.  Those handlers are never invoked.  
  
I do see in the code for `async_execute`:  
>  * It is to note that `async_execute` will us the lowest selected cancellation  
>  * type. A subprocess might ignore anything not terminal.  
  
So perhaps this is expected behaviour.  If that is the case, then what should be the approach?  
The original v1 code went directly to `kill(..., SIGINT);`

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-09-08 08:34:05 UTC  
> Url: https://github.com/boostorg/process/issues/338#issuecomment-1711289938  

I am happy to hear v2 is working for you.  
  
`total` should lead to a `SIGINT` and `partial` to a `SIGTERM`, actually: https://github.com/boostorg/process/blob/develop/include/boost/process/v2/execute.hpp#L56C1-L62C38  
  
Is that what you'd expect?  
  
Are you using something in between the cancellation and the async_execute, like an `awaitable` or a yield_context`?  
If so, these will by default filter out anything not `terminal`, which might explain your issue. If not, then it's a bug in my code.

---

## Comment 2

> Username: Chardrazle  
> Created at: 2023-09-09 14:19:17 UTC  
> Updated at: 2023-09-09 14:20:12 UTC  
> Url: https://github.com/boostorg/process/issues/338#issuecomment-1712522576  

That is what I was expecting, but the child process does not appear to be receiving the `total` nor `partial`; the `terminal` is the one that does get through, and then it pulls the plug.  
  
One thing of note: I am running the _asio_ context in a thread - maybe that is being treated differently?    
The _bash_ script never receives the `SIGINT`.  If I drop to a shell and run "kill -s INT \<proc\>" then it does.    
  
I've stripped back the code to a test-case; please take a look to see if I've made an incorrect assumption.    
(renamed as .txt because cpp was not allowed !)    
[bpv2_sig.cpp.txt](https://github.com/boostorg/process/files/12565991/bpv2_sig.cpp.txt)  
  
----  
g++ -W -Wall -pedantic -std=c++20 bpv2_sig.cpp -lpthread  
g++ --version: g++ (GCC) 11.2.1 20211105

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-09-11 02:56:54 UTC  
> Url: https://github.com/boostorg/process/issues/338#issuecomment-1713083425  

Found it. The `async_execute` uses a composed op, which internally has a cancellation_state, that filters out anything not terminal. I'll have a patch in a few minutes.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-09-11 03:42:21 UTC  
> Url: https://github.com/boostorg/process/issues/338#issuecomment-1713112103  

@Chardrazle does [a3259dc](https://github.com/boostorg/process/commit/a3259dc5f774c3c87a4230d2e3cf0eeed1a686ab) fix it for you?

---

## Comment 5

> Username: Chardrazle  
> Created at: 2023-09-11 10:21:47 UTC  
> Url: https://github.com/boostorg/process/issues/338#issuecomment-1713599630  

I tried against the sample program provided here and do not see any different behaviour.

---

## Comment 6

> Username: Chardrazle  
> Created at: 2023-10-05 06:18:40 UTC  
> Updated at: 2023-10-05 06:47:22 UTC  
> Url: https://github.com/boostorg/process/issues/338#issuecomment-1748159550  

@klemens-morgenstern sorry, maybe my comment was misinterpreted.  
When I said I didn't see any different behaviour, I meant that the patch did not make any difference; i.e. it does _not_ fix the issue as exhibited in the test case that is provided here.  
  
```  
$ uname -a  
Linux xxxxxxx 5.10.0-25-amd64 #1 SMP Debian 5.10.191-1 (2023-08-16) x86_64 GNU/Linux  
```

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2023-10-05 11:07:58 UTC  
> Url: https://github.com/boostorg/process/issues/338#issuecomment-1748670622  

Sure, but I fixed a bug and have a test showing it sends SIGINT & SIGTERM.   
  
I think we'd need to dig further down into your code then.
