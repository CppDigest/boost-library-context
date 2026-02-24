# #917 - can Support SNI in HTTPS server? [Closed]

> Username: zynfly  
> Created at: 2017-12-01 17:06:51 UTC  
> Updated at: 2017-12-07 12:09:14 UTC  
> Closed at: 2017-12-05 15:55:13 UTC  
> Url: https://github.com/boostorg/beast/issues/917  

many domain name  in a web application, and it using SSL .  how do?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-01 17:09:44 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-348551964  

https://github.com/boostorg/beast/blob/bfd4378c133b2eb35277be8b635adb3f1fdaf09d/example/http/client/sync-ssl/http_client_sync_ssl.cpp#L66

---

## Comment 2

> Username: zynfly  
> Created at: 2017-12-01 17:13:25 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-348552904  

i want use beast to write web application support sni, not a client.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-12-01 19:34:43 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-348593477  

As with the client example shown above, for servers you will need to interact directly with the OpenSSL APIs. The answer to the question on this page provides insights on how this may be accomplished:  
  
https://stackoverflow.com/questions/5113333/how-to-implement-server-name-indication-sni

---

## Comment 4

> Username: zynfly  
> Created at: 2017-12-05 15:08:31 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349332272  

nginx,apache,lighttpd and more web server can provide SNI. Although beast is a library, rather than a complete server, if you can provide SNI built-in, I think this can make many people like this library,and the work is simple.I know that ASIO does not provide SNI, but for many small and medium-sized start-ups, especially those providing web services, this is very important, at least in China.   
I know that calling OpenSSL's native API can solve this problem, but most people don't know it.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-12-05 15:11:20 UTC  
> Updated at: 2017-12-05 15:11:38 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349333148  

>if you can provide SNI built-in  
  
If I was to do that, then Beast would require OpenSSL. There are lots of people who use Beast without using OpenSSL, and the change you propose would affect them negatively. Furthermore, Beast is a low-level library. It is not a server or a client. The feature that you are asking for is out of scope for Beast, and belongs in a higher level library such as a server or connection manager. It is possible that I might write such a library in the future, in which case I will certainly address SNI.

---

## Comment 6

> Username: zynfly  
> Created at: 2017-12-05 15:19:44 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349335787  

OK.I know that beast is an low-level library.  You can not integrate OpenSSL into beast. Can you consider giving an example of a server that supports SNI? When I was popularizing C++ for web services in small and medium enterprises, the most question I was asked about was how to use multiple domains with one server. Most programmers only have boost.asio, and don't care about how OpenSSL works.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-12-05 15:21:06 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349336202  

>Can you consider giving an example of a server that supports SNI  
  
I don't know how to do that

---

## Comment 8

> Username: zynfly  
> Created at: 2017-12-05 15:23:22 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349336939  

https://github.com/boostorg/beast/blob/bfd4378c133b2eb35277be8b635adb3f1fdaf09d/example/http/client/sync-ssl/http_client_sync_ssl.cpp  
  
like this?  
  
but a sever example?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-12-05 15:25:06 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349337494  

>like this?  
  
Well it isn't quite like that now is it? Because the server has to *read* the SNI string from the OpenSSL data and decide on which virtual server to route the request to?

---

## Comment 10

> Username: zynfly  
> Created at: 2017-12-05 15:30:51 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349339461  

no route,only different servername for different digital certificate.How does the route work is something that the user (the programmer) should consider.  
Their questions are only one web application (a program) for most domain name.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-12-05 15:31:36 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349339710  

You're telling me that the server has to read the SNI string from the client, and then decide which SSL certificate to present in the handshake?

---

## Comment 12

> Username: zynfly  
> Created at: 2017-12-05 15:32:28 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349339975  

yes. only handshake.

---

## Comment 13

> Username: zynfly  
> Created at: 2017-12-05 15:32:36 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349340027  

In fact, I think this issue can go to ASIO. What's your opinion?

---

## Comment 14

> Username: vinniefalco  
> Created at: 2017-12-05 15:35:39 UTC  
> Updated at: 2017-12-05 15:36:29 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349341074  

Implementing correct examples which deal with SSL certificates is difficult and in some cases impossible. Getting the certificates to be recognized as valid in a browser or client is a problem, because the certificates that come with the examples are self-signed. And since there is no meaningful domain name (the examples bind to the IP address specified on the command line) there's no way to produce a valid certificate signed by a real certificate authority (like Verisign or many others).  
  
Delivering just the one self-signed server certificate as currently demonstrated in the examples is hard enough, but attempting to choose between several of them goes way beyond my comfort zone. And it is also outside the scope of Beast.  
  
>I think this issue can go to ASIO. What's your opinion?  
  
I can't speak for Asio but in my opinion this feature belong in a library that specifically aims to implement a server (which Beast and Asio do not). I have plans for eventually offering a server library but that is at least 5 years away.

---

## Comment 15

> Username: zynfly  
> Created at: 2017-12-05 15:48:45 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349345744  

Most of the China entrepreneurial companies are doing their own platform (PAAS), most of the scene is that, to provide a web service, such as takeaway app, other startups can resolve their own domain name to the platform, and provides its own certificate, so, to look like a number of platforms, but in fact is to provide a service platform of a company.  
  
Maybe China is not the same as your environment.  
  
then can iwrite that and upload to my github?

---

## Comment 16

> Username: vinniefalco  
> Created at: 2017-12-05 15:51:01 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349346504  

>then can iwrite that and upload to my github?  
  
I'm always happy to see people build new libraries and applications with Beast!

---

## Comment 17

> Username: zynfly  
> Created at: 2017-12-05 15:51:31 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349346681  

only exp.

---

## Comment 18

> Username: zynfly  
> Created at: 2017-12-05 15:53:00 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349347185  

I'll consider the next project with beast and let more companies use it. All I've been using is my own library, and cinatra.

---

## Comment 19

> Username: zynfly  
> Created at: 2017-12-05 15:54:55 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349347841  

i'll close this issue,and I'm going to bed. It's been too late in China.  
  
It's a pleasure to communicate with you

---

## Comment 20

> Username: vinniefalco  
> Created at: 2017-12-05 15:57:01 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349348486  

>It's a pleasure to communicate with you  
  
Likewise, thank you for your comments and support for Beast!

---

## Comment 21

> Username: zynfly  
> Created at: 2017-12-05 16:00:18 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349349610  

Cheers for C++. bye~

---

## Comment 22

> Username: djarek  
> Created at: 2017-12-06 22:04:33 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349789983  

Somewhat related issue that was opened in ASIO:  
https://github.com/chriskohlhoff/asio/issues/262

---

## Comment 23

> Username: zynfly  
> Created at: 2017-12-07 12:09:14 UTC  
> Url: https://github.com/boostorg/beast/issues/917#issuecomment-349949208  

thanks.
