# #1297 - Beast library gives a lot of warnings in Xcode on macOS [Closed]

> Username: runesig  
> Created at: 2018-11-11 21:17:30 UTC  
> Updated at: 2018-11-12 14:39:16 UTC  
> Closed at: 2018-11-12 06:13:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1297  

I have used this library for a couple of projects on Windows and I love it. Now I am trying to get it to work on Mac OS. And It does work. However, it gives me a lot of warnings when I try to compile one of the http Beast examples. It is probably me doing something wrong but I can't figure out what it is.   
  
1. Empty paragraph passed to '@see' command  
2. Possible misuse of comma operator here  
3. Implicit conversion loses integer precision: 'std::size_t' (aka 'unsigned long') to 'socklen_t' (aka 'unsigned int')  
  
<img width="1512" alt="screen shot 2018-11-11 at 22 11 24" src="https://user-images.githubusercontent.com/2069630/48318345-84aab000-e5ff-11e8-9c62-3b365f9fcca8.png">  
<img width="1512" alt="screen shot 2018-11-11 at 22 15 38" src="https://user-images.githubusercontent.com/2069630/48318346-8c6a5480-e5ff-11e8-96d6-6449dc96114c.png">  
<img width="1512" alt="screen shot 2018-11-11 at 22 15 47" src="https://user-images.githubusercontent.com/2069630/48318348-92603580-e5ff-11e8-987f-775964796d01.png">

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-11 21:20:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1297#issuecomment-437706001  

Implicit conversion warning is happening inside Asio not Beast.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-11-11 21:22:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1297#issuecomment-437706169  

Empty paragraph warning is happening on a comment, not on code. No idea why Xcode has decided to issue warnings about comments!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-11-11 21:23:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1297#issuecomment-437706218  

As for the comma, Xcode is simply wrong here. The code is correct as written. What is more likely is, that it is Xcode that is confused about the metaprogramming construct.

---

## Comment 4

> Username: runesig  
> Created at: 2018-11-12 06:13:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1297#issuecomment-437768189  

Thanks Vinnie. I was able to suppress the comment by setting the warnings "Suspicious Comma" and "Documentation Comments" to "No". From your comments I do think those are safe to ignore. That leaves me with the "Implicit Conversion" warning. That is coming from ASIO as you mentioned. So I reported it there instead.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-11-12 13:17:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1297#issuecomment-437876171  

Those "suspicious" commas though... lol

---

## Comment 6

> Username: runesig  
> Created at: 2018-11-12 14:39:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1297#issuecomment-437904809  

Yep, very suspicious, but they have probably not committed a crime.
