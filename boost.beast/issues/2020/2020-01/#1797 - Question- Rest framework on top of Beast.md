# #1797 - Question: Rest framework on top of Beast [Closed]

> Username: overthetop  
> Created at: 2020-01-04 19:51:34 UTC  
> Updated at: 2020-02-14 20:17:18 UTC  
> Closed at: 2020-02-14 18:42:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1797  

Hi all. @vinniefalco thank you for that wonderful library. I'm wondering what will be the most popular rest framework on top of beast at the moment? The only one I've found so far is that https://github.com/0xdead4ead/BeastHttp which is way less popular than the beast project itself :(. I'm wondering if exists something simular with better adoption. I'm looking for a popular library that provides ease of development and high performance.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-01-04 19:52:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1797#issuecomment-570814532  

What kind of REST? Do you mean JSON-RPC over HTTP? JSON-RPC over WebSocket? Something else? Are you looking for a client or a server?  
  
Perhaps one of these projects will suit your needs:  
https://github.com/boostorg/beast/wiki/Companies-and-Individuals-Using-Beast

---

## Comment 2

> Username: overthetop  
> Created at: 2020-01-04 20:14:57 UTC  
> Updated at: 2020-01-04 20:15:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1797#issuecomment-570816044  

Hi @vinniefalco I'm looking for a way to quickly build REST APIs. This is the most common way to build a Web API currently. That would be a quick way to handle HTTP GET, POST, DELETE, PUT etc. requests. See this link for a comparison to RPC API https://blog.jscrambler.com/rpc-style-vs-rest-web-apis/  
I want to use c++ for building microservices that will be accessible from a web browser over http. Http server and client of course. This will show off all the benfits of c++ in building modern cloud architectures.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-01-04 22:13:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1797#issuecomment-570824374  

That page has a great explanation, thanks! I am not really an expert on these matters.

---

## Comment 4

> Username: overthetop  
> Created at: 2020-01-08 17:38:55 UTC  
> Updated at: 2020-01-08 17:44:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1797#issuecomment-572177214  

Thx for the link @vinniefalco Beast can be used to build a REST API but the code is just too verbose and that makes it difficult. Hope that in future a higher level framework on top of beast will emerge and will get wildly adopted in the c++ world. Currently there isn't such a solution and that's really sad because everybody want to build for the web in 2020. Perhaps if such a framework gets into boost, that will surely become main-stream.

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-01-08 17:47:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1797#issuecomment-572180473  

@overthetop watch this space. In the meantime, this may help: https://github.com/LeonineKing1199/foxy

---

## Comment 6

> Username: stale[bot]  
> Created at: 2020-02-07 18:02:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1797#issuecomment-583530914  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: stale[bot]  
> Created at: 2020-02-14 18:42:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1797#issuecomment-586417615  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 8

> Username: kervinpierre  
> Created at: 2020-02-14 20:17:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1797#issuecomment-586456370  

for prosperity: https://github.com/expresscpp/expresscpp
