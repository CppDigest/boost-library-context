# #1670 - [feature request] Internal Logging [Closed]

> Username: dmorilha-twilio  
> Created at: 2019-07-31 17:48:39 UTC  
> Updated at: 2019-09-09 21:40:00 UTC  
> Closed at: 2019-09-08 03:58:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1670  

Our SDK uses boost beast 248 and we are very satisfied with the library!  
  
Please, correct me if I am wrong but I after a quick investigation, I could not find wether boost beast / asio does log its internal events.  
  
We would be interested in exposing any possible and useful internal detail to our consumers through our logging facilities.  
  
Any suggestions or guidance on this area would be highly appreciated as well.  
  
Thanks and keep up the great work!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-07-31 18:54:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1670#issuecomment-516976490  

> I could not find wether boost beast / asio does log its internal events.  
  
What internal events do you want to log?  
  
Asio has this:  
https://www.boost.org/doc/libs/1_70_0/doc/html/boost_asio/overview/core/handler_tracking.html

---

## Comment 2

> Username: dmorilha-twilio  
> Created at: 2019-08-02 00:22:35 UTC  
> Updated at: 2019-08-02 02:29:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1670#issuecomment-517502508  

This was a super useful start! Thanks @vinniefalco!

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-09-01 03:02:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1670#issuecomment-526881577  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-09-08 03:58:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1670#issuecomment-529167350  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 5

> Username: dmorilha-twilio  
> Created at: 2019-09-09 21:36:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1670#issuecomment-529677041  

We realized the macro expansion doesn't work nicely if other components of your software happen to include boost asio without the same expanded macro. Advices?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-09-09 21:40:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1670#issuecomment-529677982  

Hmm... I am not sure. You could write your own stream layer that logs, see:  
https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/using_io/layered_streams.html  
  
You might also implement your own executor that supports logging.
