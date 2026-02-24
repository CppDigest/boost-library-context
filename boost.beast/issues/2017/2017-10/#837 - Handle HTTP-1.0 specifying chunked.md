# #837 - Handle HTTP/1.0 specifying chunked [Open]

> Username: vinniefalco  
> Created at: 2017-10-26 01:33:02 UTC  
> Updated at: 2019-07-02 03:09:55 UTC  
> Url: https://github.com/boostorg/beast/issues/837  

Maybe generate an error during parse? Or return `false` from `message::chunked`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-18 20:59:35 UTC  
> Url: https://github.com/boostorg/beast/issues/837#issuecomment-345470784  

It seems that we want the parser to finish but then inform the caller that the semantics of the message are illegal so they can generate an appropriate HTTP response

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-10-28 23:48:52 UTC  
> Url: https://github.com/boostorg/beast/issues/837#issuecomment-433753947  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: TechnikEmpire  
> Created at: 2019-07-02 03:08:40 UTC  
> Updated at: 2019-07-02 03:09:55 UTC  
> Url: https://github.com/boostorg/beast/issues/837#issuecomment-507500201  

Just FYI there's a lot of really dumb issues like this that happen and they even come from enterprise class services. Kijiji.com for a long time sent out totally illegal headers that would cause the http-parser project to fail at parsing.  
  
When I'd tell my clients (I write http proxies) that this was the case, they didn't care because "it works on chrome" without the proxy so. Just FYI RFCs are great but you sadly very often need to need to throw proper compliance out the window to make things work. This is what all the big boys (browsers) do as well.  
  
I know that's hard for a c++ guy to swallow because we very much like our strict standard, portable compliance but it just isn't there in http land.
