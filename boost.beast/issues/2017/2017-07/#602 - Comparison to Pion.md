# #602 - Comparison to Pion [Closed]

> Username: vinniefalco  
> Created at: 2017-07-05 13:59:21 UTC  
> Updated at: 2017-08-16 00:08:32 UTC  
> Closed at: 2017-08-16 00:08:32 UTC  
> Url: https://github.com/boostorg/beast/issues/602  

>Since the documentation features comparison with other libraries, it may be worth comparing pion (https://github.com/splunk/pion) as well.

---

## Comment 1

> Username: mika-fischer  
> Created at: 2017-07-05 14:08:50 UTC  
> Url: https://github.com/boostorg/beast/issues/602#issuecomment-313113880  

While you're at it, a comparison to uWebSockets would also be interesting  
https://github.com/uNetworking/uWebSockets

---

## Comment 2

> Username: ksergey  
> Created at: 2017-07-05 18:41:08 UTC  
> Url: https://github.com/boostorg/beast/issues/602#issuecomment-313190592  

Also with libwebsockets

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-05 18:45:37 UTC  
> Url: https://github.com/boostorg/beast/issues/602#issuecomment-313191738  

LOL come on now guys, do you want me writing code or writing comparisons? :)

---

## Comment 4

> Username: mika-fischer  
> Created at: 2017-07-06 07:37:38 UTC  
> Url: https://github.com/boostorg/beast/issues/602#issuecomment-313320229  

I would find a comparison to uWebSockets interesting, since it's similar in scope, is modern C++ and claims to be much faster, more memory-efficient and more scalable than beast.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-06 07:41:48 UTC  
> Updated at: 2017-07-06 10:08:56 UTC  
> Url: https://github.com/boostorg/beast/issues/602#issuecomment-313321099  

Well there's not much to say, uWebSockets is non-Asio and hardly Modern C++. It does achieve better performance and memory usage, although there are questions about whether the benchmarks measure real-world use. As uWebSockets doesn't track any networking standard, I don't feel particularly motivated to form a comparison.  
  
I have a lot of work to do on Beast, and I need to be careful with priorities!

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-08-16 00:08:32 UTC  
> Url: https://github.com/boostorg/beast/issues/602#issuecomment-322622498  

I'm going to close this since its a bit out of scope in the near to medium term but we can revisit it later
