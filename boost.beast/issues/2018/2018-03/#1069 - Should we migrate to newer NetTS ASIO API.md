# #1069 - Should we migrate to newer NetTS ASIO API? [Closed]

> Username: vchang-akamai  
> Created at: 2018-03-16 22:16:56 UTC  
> Updated at: 2018-04-24 20:44:29 UTC  
> Closed at: 2018-04-24 20:44:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1069  

just a general question, not a bug report:  
  
I started using Beast about a year ago with the legacy ASIO APIs (io_service, io_service::strand, boost::bind, boost::asio::placeholders, etc) before the new NetTS APIs were released. In Dec, the first official Beast was released in Boost and it also coincided with the first release of NetTS-style ASIO.  
  
I'm wondering if it's recommended to update my code base to use the newer NetTS-style ASIO APIs. How long are the legacy ASIO API going to be supported by ASIO and Beast? (I'm not familiar with the roadmap for NetTS and the state of activities there... Are there still likely to be changes in NetTS APIs at this point?)

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1069#issuecomment-384003541  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: djarek  
> Created at: 2018-04-24 18:35:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1069#issuecomment-384035942  

I'd say that you should upgrade to the new API at earliest convenience to get some of the nice features of the new API (e.g. support for move-only CompletionHandlers). While the old API is deprecated, I don't expect it to disappear any time soon, because ASIO has had fairly good long-term stability of its API in the past.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-04-24 19:32:08 UTC  
> Updated at: 2018-04-24 19:32:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1069#issuecomment-384052645  

If history is any indication, the legacy Asio APIs will likely be supported for a very long time. There is little upside to removing them, and the downside is significant (breaking everyone's code that uses the legacy interfaces). I would expect that the Asio author will try to maintain them for as long as possible. That said, my personal preference is to always use the most recent interfaces when possible. Beast will support the legacy interfaces as long as Asio supports them.

---

## Comment 4

> Username: vchang-akamai  
> Created at: 2018-04-24 20:44:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1069#issuecomment-384073777  

ok, thanks for the feedback!
