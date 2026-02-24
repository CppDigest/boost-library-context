# #88 - Is there any example for chunked transfer encoding ? [Closed]

> Username: malickf  
> Created at: 2018-02-17 16:44:41 UTC  
> Updated at: 2021-03-23 11:18:59 UTC  
> Closed at: 2018-02-17 18:10:00 UTC  
> Url: https://github.com/boostorg/asio/issues/88  

I'm developing a client that receive HTTPS response by [chunked transfer encoding](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) , I found the example https://github.com/boostorg/asio/blob/6814d260d02300a97521c1a93d02e30877fb8ff5/example/cpp03/ssl/client.cpp#L2 very useful unfortunately it does not use the Chunked transfer encoding protocol. Do you know if there is an example of a client, made with asio that support this protocol ?   
I have tried to play with ` boost::asio::transfer_exactly` by providing the received chunk size without success.  
Thanks,   
Malick  
  
Edit: in short I'm looking for an example that receive asynchronously a response and that can disentangle the chuck size from the chunk body.

---

## Comment 1

> Username: malickf  
> Created at: 2018-02-17 18:09:59 UTC  
> Url: https://github.com/boostorg/asio/issues/88#issuecomment-366460121  

I'm realizing that asio is low level oriented, so I guess that my "issue" may be irrelevant here... I probably need to handle the chunked content format myself.... So I'm closing this issue.

---

## Comment 2

> Username: abcdef123ghi  
> Created at: 2021-03-23 11:18:59 UTC  
> Url: https://github.com/boostorg/asio/issues/88#issuecomment-804821500  

> I'm realizing that asio is low level oriented, so I guess that my "issue" may be irrelevant here... I probably need to handle the chunked content format myself.... So I'm closing this issue.  
  
Hi folks   
  I have same problem as well , could you please share your code with me? Thank you so much
