# #812 - LAST CALL FOR CHANGES BEFORE BOOST 1.66.0 !!!! [Closed]

> Username: vinniefalco  
> Created at: 2017-10-11 18:48:54 UTC  
> Updated at: 2017-12-02 14:40:58 UTC  
> Closed at: 2017-12-02 14:40:58 UTC  
> Url: https://github.com/boostorg/beast/issues/812  

Beast is scheduled for its initial release in Boost 1.66.0. The cutoff for making code changes is October 25th. If anyone has an issue they think needs to be addressed, no matter if it is cosmetic, a bug, something with the documentation, or a feature, then they need to speak up now by opening an issue - I will get to it right away if I can! Otherwise, what we have now at the tip of **master** is what will ship!

---

## Comment 1

> Username: syedalamabbas  
> Created at: 2017-10-12 04:06:38 UTC  
> Updated at: 2017-10-14 14:56:39 UTC  
> Url: https://github.com/boostorg/beast/issues/812#issuecomment-336015110  

Great work! I stumbled upon this after going through other libraries such as Microsoft's cpprestsdk (https://github.com/Microsoft/cpprestsdk), libsourcey( https://sourcey.com/libsourcey/), libwebsockets (https://github.com/warmcat/libwebsockets) and websocketapp (https://github.com/zaphoyd/websocketpp). Yours is the best one, I am going to use it in my project, thanks.  
  
Alam

---

## Comment 2

> Username: generationtech  
> Created at: 2017-10-12 04:16:27 UTC  
> Url: https://github.com/boostorg/beast/issues/812#issuecomment-336016187  

^Ditto. I went through all that stuff too without success before finding Vinnie's work. Beast websockets is a lynchpin for an important project of mine. Kudos to 1.66.0

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-12 11:21:02 UTC  
> Url: https://github.com/boostorg/beast/issues/812#issuecomment-336098431  

I appreciate the kind words! "libsourcey" that's a new one...and quite a lot of features on that library!

---

## Comment 4

> Username: bitm4ster  
> Created at: 2017-10-14 17:57:08 UTC  
> Url: https://github.com/boostorg/beast/issues/812#issuecomment-336652318  

Thank you for your amazing contribution to the open source community!

---

## Comment 5

> Username: fcharlie  
> Created at: 2017-10-27 02:53:32 UTC  
> Updated at: 2017-10-27 03:13:54 UTC  
> Url: https://github.com/boostorg/beast/issues/812#issuecomment-339858468  

I do not think that beast is suitable for the `Restful Dev` or `Git (Stream Transfer) Http Dev` Beast is slightly more complex to use, beast should be added to such simple Feature as Golang .NET `HttpClient`, and Golang `HttpServer` .Net  `KestrelHttpServer`.   
  
I use C + + to write `Git Smart Http Server`, which require Http Server library and HttpClient library (authentication required).   
After examining most of the C + + HTTP server libraries, I finally had to implement HTTP server myself.  
httpclient use cpprestsdk.   
  
Git HTTP Server need unpack Http Request body write to git-upload-pack/git-recieve-pack stdin, Copy git-upload-pack/git-receive-pack stdout as Http Response body. Golang `io.Copy/io.CopyBuffer` It is easy to solve this function.  
When will beast be able to implement this function in such a simple way?  
   
Then I spent a few days using Golang to implement the Git HTTP server (No OpenSource).  
  
This is my Git Server based on .Net(KestrelHttpServer) implementation: https://github.com/fcharlie/Angelo  
  
Does beast plan to enter the C + + standard library? If you want a standard, I suggest that you consider the needs of the average developer.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-10-27 12:55:15 UTC  
> Url: https://github.com/boostorg/beast/issues/812#issuecomment-339963454  

>When will beast be able to implement this function in such a simple way?  
  
This is addressed in the FAQ  
http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/design_choices/faq.html

---

## Comment 7

> Username: liuaifu  
> Created at: 2017-11-09 13:18:02 UTC  
> Url: https://github.com/boostorg/beast/issues/812#issuecomment-343151601  

When will release 1.66 beta1?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-11-09 14:19:10 UTC  
> Url: https://github.com/boostorg/beast/issues/812#issuecomment-343167829  

>When will release 1.66 beta1?  
  
@pdimov ?

---

## Comment 9

> Username: pdimov  
> Created at: 2017-11-09 14:22:44 UTC  
> Url: https://github.com/boostorg/beast/issues/812#issuecomment-343168916  

See the calendar at http://www.boost.org/development/index.html

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-11-09 16:26:06 UTC  
> Url: https://github.com/boostorg/beast/issues/812#issuecomment-343209347  

Looks like November 15

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-12-02 14:40:58 UTC  
> Url: https://github.com/boostorg/beast/issues/812#issuecomment-348696317  

Boost 1.66.0 is on schedule for December 13th release. It is about to be closed for changes, thanks everyone!
