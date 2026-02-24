# #1277 - Sessions leaks when it has too many concurrent requests [Closed]

> Username: bog-dan-ro  
> Created at: 2018-10-27 07:25:17 UTC  
> Updated at: 2018-11-28 17:28:14 UTC  
> Closed at: 2018-11-28 17:28:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1277  

I'm using https://github.com/lighttpd/weighttp.git to benchmark beast and I found that it hangs when I'm using more than 1000 concurrent connections (CC).  
  
Setup info:  
 - debian unstable  
 - boost 1.67 (from debian's packages)  
 - attached [boost_beast.zip](https://github.com/boostorg/beast/files/2521527/boost_beast.zip) srcs.  
  
Steps to reproduce the issue:  
 - clone & build weighttp  
 - set file limit to 1m `$ sudo sysctl -w fs.file-max=1000000`   
 - run test  
 - make sure the test is running `curl -vv http://localhost:8081`  
 - run benchmark 100 CC  ``./weighttp -n 1000000 -c 100 -t `nproc --all` -k localhost:8081`` (it should pass)  
 - run benchmark 1000 CC ``./weighttp -n 1000000 -c 1000 -t `nproc --all` -k localhost:8081`` (it should pass)  
 - run benchmark 5000 CC ``./weighttp -n 1000000 -c 5000 -t `nproc --all` -k localhost:8081`` it hangs on my computer   
 - run benchmark 30000 CC ``./weighttp -n 1000000 -c 30000 -t `nproc --all` -k localhost:8081`` it hangs on my computer

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-10-27 15:58:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1277#issuecomment-433632560  

hmm....not sure why it isn't working for you, I have tested that server using **ab** (Apache Bench) with tens of thousands of connections and it works great. Does the unmodified example also hang? Perhaps try the **master** branch to see if something was fixed which is causing your current issue.

---

## Comment 2

> Username: bog-dan-ro  
> Created at: 2018-10-28 06:23:28 UTC  
> Updated at: 2018-10-28 08:41:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1277#issuecomment-433679984  

Did you tried also with `weighttp`?  
ab is single threaded and it doesn't push it to the edge. I'll try with master and report the results.

---

## Comment 3

> Username: bog-dan-ro  
> Created at: 2018-10-28 08:58:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1277#issuecomment-433687754  

I test it again it with boost master and I can confirm that it **leaks** sessions using the `weighttp` tool (there are a few sessions that are keep running **after** `weighttp` exits.  
Because I used a different computer (much more powerful than my laptop)  I had to tweak the concurrent connections a little bit to catch the error.  
  
Attached [test_beast.zip](https://github.com/boostorg/beast/files/2522607/test_beast.zip) new changes to srcs (to your *unmodified example* I added the activeSessions log, same change tyo mine). As I expected both have the same problem.  
  
Let me know if you need more info to help you to reproduce the problem.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-10-29 05:32:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1277#issuecomment-433793000  

Someone was able to reproduce it with your setup, but I am going to need you to dig deeper. There is no reason why the example code shouldn't work, this code has been around for a year and no one has had a problem with it. I suspect that the problem is with your environment. If you can debug it and find out why it seems to be hanging, that would be a great help.

---

## Comment 5

> Username: bog-dan-ro  
> Created at: 2018-10-29 06:52:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1277#issuecomment-433805163  

I changed the name to reflect the problem better.  
So the sessions are **leaking**, the constructor is called but the destructor never, **not hanging**, the event loops, io_context,  are processing the events and they are not hanged in some mutext or something.  
  
> Someone was able to reproduce it with your setup  
  
You don't need to use the **same** setup, I pretty sure that your linux distribution of choice will do.  
  
> , but I am going to need you to dig deeper.   
  
Well, I stumbled on this problem while I was evaluating if I can use beast to replace some parts from my own HTTP server, and I thought it will be nice to report this problem ;-), but sadly I don't have enough time to digg deeper. My **hunch** is that asio doesn't detect when a socket is dead, so it will never be able to destroy the session, so you might want to implement some kind of timeouts (e.g. if you're in the middle of req/res and in 1s there is no communication you close the session and the socket). IIRC I had a similar problem and this is how I fixed it.  
  
> There is no reason why the example code shouldn't work, this code has been around for a year and no one has had a problem with it.  
  
Hehe probably nobody tried very hard to find this kind of problems ;-)  
  
>  I suspect that the problem is with your environment. [...]  
  
As I mentioned in my previous comment I used two environments to be sure my first one is not the problem. Also as long as `Someone` **else** `was able to reproduce` the issue, then we can exclude my environment ;-).

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-10-29 12:10:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1277#issuecomment-433886931  

> you might want to implement some kind of timeouts  
  
Ah! This explains everything. This particular example is not intended to have timeouts, as adding those in would complicate the code. Timeouts are implemented in the "advanced" server examples, here:  
https://github.com/boostorg/beast/tree/f4c3e52a8cabefc97fbe3884b2675a784cf2bed2/example/advanced  
  
I have been working on a stream I/O object that works like a regular Asio socket and has built-in support for timeouts, you can see that work here:  
https://github.com/vinniefalco/beast/blob/3e96c4f6a218c89b577b51461f0bd2fb85196329/include/boost/beast/experimental/core/timeout_socket.hpp#L33  
  
If you would like to try the advanced server it should resolve this issue. Please use the tip of the master branch since a bug in timeouts was recently fixed there. Thanks!

---

## Comment 7

> Username: djarek  
> Created at: 2018-10-29 15:49:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1277#issuecomment-433963283  

@bog-dan-ro   
I never observed sessions leaking in the code you posted. A few sessions took a bit longer to close, I suspect you didn't wait long enough (they closed after about a minute in my setup).

---

## Comment 8

> Username: bog-dan-ro  
> Created at: 2018-10-29 16:29:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1277#issuecomment-433979440  

@djarek well, I waited ~1 minute, which IMHO is way too much, and indeed a few were closed but not all. Why IMHO 1 minute is way too much, last time I checked **ab** & **weighttp** could do over 150k requests per second. Imagine that a bad guy will use this technique to fill all the connections, and this way he can easily create a {D}DoS attack.  
  
If this weekend I'll have some free time, I'll check it again with @vinniefalco suggestions.  
@vinniefalco will be great if you can create a simple http server with timeouts and log the active sessions on stdout so I can test it without creating one or doing changes to your existing ones.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-10-29 21:21:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1277#issuecomment-434084943  

> will be great if you can create a simple http server with timeouts  
  
Yes, as I said in my previous message, the "advanced" servers come with timeouts:  
https://github.com/boostorg/beast/tree/f4c3e52a8cabefc97fbe3884b2675a784cf2bed2/example/advanced

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-11-28 17:28:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1277#issuecomment-442533374  

Looks like this is resolved!
