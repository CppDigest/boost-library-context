# #441 - Review: explore IRIs [Open]

> Username: alandefreitas  
> Created at: 2022-08-22 05:48:28 UTC  
> Updated at: 2024-11-11 18:59:32 UTC  
> Url: https://github.com/boostorg/url/issues/441  

> The lack of IRI support is unfortunate.  It's 2022; we should all  
be writing software with Unicode support by default.  However, this can  
be built on top of Boost.URL, and isn't needed in all cases.  
  
What are the use-cases for IRI?  
  
From my point of view, all I can do is study IRIs more.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-08-22 06:46:39 UTC  
> Url: https://github.com/boostorg/url/issues/441#issuecomment-1221921260  

> all I can do is study IRIs more  
  
More and more I've been noticing how this very true. I can't even evaluate if some of the comments on IRIs make sense. I can only answer them by studying IRIs.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-09-28 21:50:53 UTC  
> Url: https://github.com/boostorg/url/issues/441#issuecomment-1261501276  

I read a little more about IRIs and I don't think we should touch this until all else is stable.  
  
Some changes we would need:  
  
- Allow URLs to contain unicode chars or a new type to allow these chars  
- A function to convert IRIs to URIs: it does (i) [unicode normalization](https://en.wikipedia.org/wiki/Unicode_equivalence), (ii) encode to UTF-8, and (iii) percent-encode bytes left.  
  
Design:  
  
- `url_view`s would be allowed to additional unicode chars or we would need another `iri_view` class;  
- Boost.text could help with many things here. The conversions might actually become trivial with boost.text. So trivial that they might not even be needed for converting to URL.

---

## Comment 3

> Username: petrepircalabu  
> Created at: 2023-03-15 09:38:47 UTC  
> Url: https://github.com/boostorg/url/issues/441#issuecomment-1469668898  

I have a question related to the use cases of IRIs. Are there any requirements in the HTTP related RFCs (9112 and complementary) that a server MUST support non-ascii request targets without percent encoding?  
e.g.: When using curl to send a request for a non-ascii resource the target is not percent encoded but an UTF-8 string:   
  
`curl -x 10.22.65.19:7074 -vvv "[http://10.17.13.30:8084/files/교회-요양원-모임고리로감염확산](http://10.17.13.30:8084/files/%EA%B5%90%ED%9A%8C-%EC%9A%94%EC%96%91%EC%9B%90-%EB%AA%A8%EC%9E%84%EA%B3%A0%EB%A6%AC%EB%A1%9C%EA%B0%90%EC%97%BC%ED%99%95%EC%82%B0)"  
*   Trying 10.22.65.19:7074...  
* Connected to (nil) (10.22.65.19) port 7074 (#0)  
> GET [http://10.17.13.30:8084/files/교회-요양원-모임고리로감염확산](http://10.17.13.30:8084/files/%EA%B5%90%ED%9A%8C-%EC%9A%94%EC%96%91%EC%9B%90-%EB%AA%A8%EC%9E%84%EA%B3%A0%EB%A6%AC%EB%A1%9C%EA%B0%90%EC%97%BC%ED%99%95%EC%82%B0) HTTP/1.1  
> Host: 10.17.13.30:8084  
> User-Agent: curl/7.81.0  
> Accept: */*  
> Proxy-Connection: Keep-Alive  
>`

---

## Comment 4

> Username: alandefreitas  
> Created at: 2023-03-15 15:40:48 UTC  
> Url: https://github.com/boostorg/url/issues/441#issuecomment-1470272970  

I don't think so. Requiring a server to accept some kind of URL is equivalent to saying the server must contain a given resource. If you have no resource to associate with `교회-요양원-모임고리로감염확산`, there's nothing to support here. Curl tends to accept loose inputs because it's a producer that can talk to any server. But I don't know if it's converting these inputs to something the server should understand or just passing the URL through. Everything usually tends to be fine when the input is converted to some proper percent escaped URL.  
  
I think there are two opinions on this.   
  
- The first opinion is that producers should be strict and consumers should be loose.    
- The second opinion is that both should be strict.  
  
One supposed advantage of the first point of view is that we would have more strict parsers over time because servers wouldn't need to handle loose input bad producers are generating. So servers could eliminate workarounds over time.   
  
- In practice, that doesn't seem to happen because not the same group of people is writing producers and consumers in a coordinated way. So the more servers accept loose input, the fewer people seem to invest in proper producers and servers need to keep investing in workarounds to handle this bad input.   
- Besides bad input, there's a problem with informal extensions to the protocol. These extensions keep coming up. It's almost impossible to keep up with all variations. Some ambiguous URLs end up having very different results with different libraries and this is bad and even dangerous sometimes.   
  
The second point of view has the advantage that bad producers and consumers are iteratively kicked out of the ecosystem until everyone complies correctly with proper input and no ambiguous variations.   
  
- This actually works because anyone not properly following the protocol needs to fix the bug immediately.   
- But we have a network effect. So the obvious disadvantage is you might not always be able to communicate with a server that doesn't adhere to the protocol.  
  
For Boost.URL, in my opinion, the second point of view seems more reasonable considering the use cases it serves, which is usually machine-to-machine communication. For instance,  
  
- If you create a server router (a consumer) with Boost.URL, your server will only route valid URLs to resources. Your server doesn't need to parse any invalid URL because they wouldn't refer to any resources and your server can communicate well with any producer without any extensions.   
- If you are using Boost.URL to make requests, it's also an advantage because even servers that implement workarounds will also accept valid requests. At worst, you might need to escape some chars before making the request.  
  
If we consider a browser address bar as the most common use case, where it's user-to-machine communication, then Boost.URL would need to accept and sanitize a lot of invalid input. Now there's no exact protocol to follow because humans can fail in lots of different ways. But that's bad for machine-to-machine communication even when it works. If Boost.URL handled that use case by default, then servers could now be routing invalid URLs to resources without meaning to and clients could also be making requests to invalid URLs.   
  
That doesn't mean Boost.URL can't help users produce valid input. For instance, that's what `urls::format` is meant to do.
