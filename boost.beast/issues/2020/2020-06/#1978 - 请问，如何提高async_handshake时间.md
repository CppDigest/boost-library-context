# #1978 - 请问，如何提高async_handshake时间 [Closed]

> Username: Yinpinghua  
> Created at: 2020-06-10 07:09:45 UTC  
> Updated at: 2020-07-18 05:48:26 UTC  
> Closed at: 2020-07-18 05:48:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1978  

请问，如何提高https证书校验的时间

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-06-10 09:24:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1978#issuecomment-641873571  

Google translates this as: "Excuse me, how to increase the time of https certificate verification"  
  
At the moment, the question is ambiguous.  
  
Does it mean:  
  
1. How do I increase the validity period of an SSL certficate? In this case, the answer is that you cannot. You will need to issue a new certificate.  
  
2. How do I increase the timeout while a client is verifying the certificate? This is difficult to answer here as if depends on the verification mechanism you are using.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2020-07-11 02:07:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1978#issuecomment-656967640  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2020-07-18 05:48:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1978#issuecomment-660431772  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
