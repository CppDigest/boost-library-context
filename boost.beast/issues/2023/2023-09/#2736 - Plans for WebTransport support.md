# #2736 - Plans for WebTransport support [Closed]

> Username: jcelerier  
> Created at: 2023-09-06 15:24:38 UTC  
> Updated at: 2024-01-11 18:10:24 UTC  
> Closed at: 2024-01-01 10:48:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2736  

Hello!   
  
Are there plans for supporting this new protocol? It has been available for a year and a half-ish in browsers: https://developer.mozilla.org/en-US/docs/Web/API/WebTransport

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-09-07 07:46:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2736#issuecomment-1709643988  

There are not. Beast is in maintenance mode.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2024-01-07 22:34:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2736#issuecomment-1880200147  

HTTP/3 lol

---

## Comment 3

> Username: jcelerier  
> Created at: 2024-01-08 17:55:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2736#issuecomment-1881566953  

I assume no chance for it in Beast (or anywhere in boost) either?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2024-01-08 22:46:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2736#issuecomment-1881940896  

HTTP/3 and SPDY are boondoggles

---

## Comment 5

> Username: jcelerier  
> Created at: 2024-01-10 15:35:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2736#issuecomment-1885088601  

I mean maybe but at some point some service is going to expose a WebTransport endpoint only and I'll want to connect to it from C++ code - or conversely, some standard protocol for $whatever will emerge on top of WebTransport which i'll want to provide a performant backend for

---

## Comment 6

> Username: jcelerier  
> Created at: 2024-01-10 15:48:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2736#issuecomment-1885114361  

e.g. there's currently standardisation effort towards some new low latency media stack for web browsers, based on WebTransport - and C++ is going to be a major implementation language for this kind of backend (low-latency multimedia processing and streaming)

---

## Comment 7

> Username: vinniefalco  
> Created at: 2024-01-11 18:10:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2736#issuecomment-1887703113  

That is very nice but the amount of work to design and implement a conforming library is way beyond my abilities, or desire.
