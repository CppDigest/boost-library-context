# #1038 - Question about GET request to API (did I do it wrong?) [Closed]

> Username: DragonOsman  
> Created at: 2018-02-22 14:35:20 UTC  
> Updated at: 2018-05-01 17:32:49 UTC  
> Closed at: 2018-05-01 17:32:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1038  

As the title says, I need to ask if I'm doing something wrong with the way I'm sending the request to the currency API I want to get rates from.    
  
The request is sent since `ec`'s message string is reporting success.  But now I'm getting a "Bad request" error from the API itself.  I've checked the URL and it seems to check out.  So I need to ask: What could I be doing wrong with my request?  
  
I made another Gist.  Link: https://gist.github.com/DragonOsman/7bbdb049ea2a77173c5dafbecd5f1be2  
  
Thanks in advance for any help.

---

## Comment 1

> Username: DragonOsman  
> Created at: 2018-02-24 13:08:12 UTC  
> Updated at: 2018-02-24 22:09:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1038#issuecomment-368227421  

Hey; sorry for the double post, but I need to give an update here.  
  
I tried to send the request manually to the same URL and port, and it worked that way.  I did it like this: `apilayer.net:80/` (and then the whole target).    
  
So why won't it work when I do it in the app?  Could it be that I shouldn't use port 80 for a client request while also running the server on port 8080 (or is that not a problem?)?  
  
Edit: I tried making the request to the API host through a separate test app that only has the client-side code (using the synchronous HTTP client example, except that I set host, port and target manually instead of using command-line arguments), and saw that it works that way.    
  
I need some help in figuring out what to do next to isolate the problem and fix it.  I'm now also initializing the `io_context` and `tcp::socket` objects in the client code itself instead of doing it in the cache storage class, by the way.    
  
I updated the code just in case.  Here's the link again (same one as before): https://gist.github.com/DragonOsman/7bbdb049ea2a77173c5dafbecd5f1be2

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-03-02 18:32:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1038#issuecomment-370011432  

I have no idea if it is correct, sorry.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1038#issuecomment-384003558  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-05-01 17:32:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1038#issuecomment-385733447  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
