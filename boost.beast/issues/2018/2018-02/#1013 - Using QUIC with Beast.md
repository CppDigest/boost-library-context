# #1013 - Using QUIC with Beast [Closed]

> Username: mcraveiro  
> Created at: 2018-02-08 13:47:51 UTC  
> Updated at: 2024-04-17 11:56:00 UTC  
> Closed at: 2018-02-08 15:09:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1013  

Hi Beast developers,  
  
Thanks very much for an extremely useful library and congratulations on making into Boost. I have a question regarding the possibility of using QUIC with Beast. QUIC is described on a LWN article [1]. It can be summarised as follows [2]:  
  
> QUIC is an experimental protocol aimed at reducing web latency over that of TCP. On the surface,   
> QUIC is very similar to TCP+TLS+SPDY implemented on UDP. Because TCP is implemented in   
> operating system kernels, and middlebox firmware, making significant changes to TCP is next to   
> impossible. However, since QUIC is built on top of UDP, it suffers from no such limitations.  
  
Is it possible to use QUIC with Beast?  
  
Many thanks for your time.  
  
Marco  
[1] https://lwn.net/Articles/745590/  
[2] https://github.com/devsisters/libquic

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-08 14:36:07 UTC  
> Updated at: 2018-02-08 14:36:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1013#issuecomment-364130187  

>Is it possible to use QUIC with Beast?  
  
You would need to implement a type which meets the requirements of **AsyncReadStream** and **AsyncWriteStream**, and then yes Beast's HTTP stream algorithms will work with QUIC. See:  
  
http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/AsyncReadStream.html  
http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/AsyncWriteStream.html  
  
I can help if you wish to understand these concepts or undertake an implementation.  
  
Regards

---

## Comment 2

> Username: mcraveiro  
> Created at: 2018-02-08 15:09:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1013#issuecomment-364140764  

Hi @vinniefalco   
  
Thanks very much for your prompt reply. I will have a play with these and see how far I can get. I will close the issue for now but reopen with specific questions if I get stuck.  
  
Cheers  
  
Marco

---

## Comment 3

> Username: alri02  
> Created at: 2024-04-11 08:38:08 UTC  
> Updated at: 2024-04-11 08:44:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1013#issuecomment-2049204919  

Hi @mcraveiro ,  
  
Were you able to integrate Beast with QUIC ? If yes, what QUIc library did you use since there are so many quic versions implemented by different people ? I want to replace Http from boost.beast with Http3 in my project.

---

## Comment 4

> Username: mcraveiro  
> Created at: 2024-04-17 11:55:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1013#issuecomment-2061088097  

I'm afraid I ended up not using C++ for my project. But let me know what you find.
