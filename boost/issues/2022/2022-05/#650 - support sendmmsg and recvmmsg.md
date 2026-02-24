# #650 - support sendmmsg and recvmmsg [Closed]

> Username: zkas  
> Created at: 2022-05-25 01:42:22 UTC  
> Updated at: 2022-05-25 07:41:50 UTC  
> Closed at: 2022-05-25 07:41:50 UTC  
> Url: https://github.com/boostorg/boost/issues/650  

is there an plan to support sendmmsg and recvmmsg on unix platform? or WSARecvMsg on windows platform?

---

## Comment 1

> Username: mclow  
> Created at: 2022-05-25 01:45:23 UTC  
> Url: https://github.com/boostorg/boost/issues/650#issuecomment-1136618825  

Which boost library are you referring to here?

---

## Comment 2

> Username: zkas  
> Created at: 2022-05-25 01:55:40 UTC  
> Url: https://github.com/boostorg/boost/issues/650#issuecomment-1136626157  

> Which boost library are you referring to here?  
  
boost_1_76_0, i searched the source code with visual studio code, but not found any keywords likes "sendmmsg" or "recvmmsg"

---

## Comment 3

> Username: mloskot  
> Created at: 2022-05-25 07:28:01 UTC  
> Url: https://github.com/boostorg/boost/issues/650#issuecomment-1136889117  

@zkas Boost is a **collection of libraries**, so once again: Which Boost library you aim with your suggestion?

---

## Comment 4

> Username: zkas  
> Created at: 2022-05-25 07:33:19 UTC  
> Url: https://github.com/boostorg/boost/issues/650#issuecomment-1136893867  

> @zkas Boost is a **collection of libraries**, so once again: Which Boost library you aim with your suggestion?  
  
asio network, i also searched boost_1_79_0, but not found too.we need "sendmmsg" or "recvmmsg" to realize   
aggregate sending and receiving data through udp socket

---

## Comment 5

> Username: mloskot  
> Created at: 2022-05-25 07:40:34 UTC  
> Updated at: 2022-05-25 07:41:02 UTC  
> Url: https://github.com/boostorg/boost/issues/650#issuecomment-1136900258  

@zkas   
> asio network,  
  
Then, 👉  https://github.com/chriskohlhoff/asio  
  
> we need "sendmmsg" or "recvmmsg" to realize aggregate sending and receiving data through udp socket  
  
Then, speak to Asio developers, not the whole Boost community.

---

## Comment 6

> Username: zkas  
> Created at: 2022-05-25 07:41:50 UTC  
> Url: https://github.com/boostorg/boost/issues/650#issuecomment-1136901426  

thank you for your replay
