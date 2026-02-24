# #1282 - boost::beast::http::verb::delete_ instead of boost::beast::http::verb::delete? [Closed]

> Username: lingtjien  
> Created at: 2018-10-31 08:51:41 UTC  
> Updated at: 2018-10-31 14:52:49 UTC  
> Closed at: 2018-10-31 14:40:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1282  

Is there a specific reason why the delete verb has an underscore while all the others don't? It seems rather inconsistent to me, so I assume there is a good reason for that, that I'm currently unaware of?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-10-31 13:09:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1282#issuecomment-434680243  

LOL.... think about it carefully :) If you haven't figured it out by tomorrow then I'll give you the answer.

---

## Comment 2

> Username: lingtjien  
> Created at: 2018-10-31 14:40:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1282#issuecomment-434712692  

Oh... reserved keywords... derp my bad   
  
(I was converting my project from a different REST library which had all capital case for the http verb so it would be GET, POST, PUT, DELETE and I wrote a script to convert all those occurrences from capital to lowercase and stumbled upon this inconsistency, hence the question, thanks)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-10-31 14:52:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1282#issuecomment-434717665  

That didn't take long!
