# #2344 - Http response parser [Closed]

> Username: gagikk  
> Created at: 2021-11-17 22:43:45 UTC  
> Updated at: 2022-06-16 14:30:39 UTC  
> Closed at: 2022-06-16 14:30:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2344  

HTTP response parser returns bad status when the reason phrase is empty and status code not followed by SP, i.e. on `HTTP/1.1 200`. Would be nice to be more flexible on this.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-11-17 22:45:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2344#issuecomment-972190140  

The ABNF for status-line is  
```  
status-line = HTTP-version SP status-code SP reason-phrase CRLF  
```  
  
This means that the SP is required, although reason-phrase can be empty. Beast adheres strictly to the RFC, and this is unlikely to change. I think it would be better to change the server which is emitting invalid HTTP, to be conforming to the RFC.

---

## Comment 2

> Username: Miksu82  
> Created at: 2021-11-19 08:29:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2344#issuecomment-973862353  

@vinniefalco I totally agree the strict standard conformance is something to strive for. But unfortunately the real world is more complicated. We are developing a proxying app that doesn't have access to the misbehaving web servers and this gives us real problems.  
  
When the boss man comes complaining that activebeat.com doesn't work when he is using our app, it is not possible to try to explain about RFCs and misbehaving web servers. He just wants it to work.   
  
Of course we could maintain our own fork of boost parser or switch to another HTTP parser but both of them come with their own complications  and you probably understand why we would like to try to avoid that.  
  
This is one of the non-standard sites  
  
```  
curl --http1.1  -D - -o /dev/null https://www.activebeat.com/ 2> /dev/null | head -1 | xxd  
00000000: 4854 5450 2f31 2e31 2032 3030 0d0a       HTTP/1.1 200..  
```  
  
Do you think you could accept a pull request where non standard parsing features would be put behind `ALLOW_NON_STANDARD_HTTP` preprocessor macro and all the features that are behind `ALLOW_NON_STANDARD_HTTP` would be well documented and of course by default `ALLOW_NON_STANDARD_HTTP` is not set?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-11-19 22:35:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2344#issuecomment-974517267  

You could filter the incoming buffer if you want, to make it conforming. An example of how to do this is here: https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/include/boost/beast/_experimental/http/icy_stream.hpp#L24

---

## Comment 4

> Username: Miksu82  
> Created at: 2021-11-23 11:19:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2344#issuecomment-976416987  

Thanks for the suggestion. Unfortunately it looks overly complicated for a relatively simple thing. And it seems we would need to parse each response status line twice.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2344#issuecomment-1008220680  

This issue has been open for a while with no activity, has it been resolved?
