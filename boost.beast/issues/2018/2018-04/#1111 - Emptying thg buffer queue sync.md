# #1111 - Emptying thg buffer queue sync [Closed]

> Username: shovalyechiel  
> Created at: 2018-04-29 09:14:29 UTC  
> Updated at: 2018-06-05 15:11:41 UTC  
> Closed at: 2018-06-05 15:11:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1111  

Hi. I need a websocket use in a not some common way. i need to read SYNC all the messages process each one of them ( individually) and then after the queue is empty do some logic. the problem is a can't find a way to do some read the only block if there is some data. is there a way to set a timeout, or perform a non blocking read?  
thanks!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-04-29 14:02:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1111#issuecomment-385253619  

Non blocking reads are accomplished by using the asynchronous interfaces. If you want a timeout, use one of the Asio deadline or steady timers.

---

## Comment 2

> Username: shovalyechiel  
> Created at: 2018-04-29 14:04:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1111#issuecomment-385253861  

ok thanks you!  
so just to make sure I got it, I can do some BLOCKING read until some multiple message arrives and then set the Asio timeout to zero ?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-04-29 14:09:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1111#issuecomment-385254205  

No that's not what I said, you need to use the asynchronous interfaces.

---

## Comment 4

> Username: shovalyechiel  
> Created at: 2018-04-29 14:10:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1111#issuecomment-385254259  

ok I will explore some more.   
thank you !

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-05-29 14:14:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1111#issuecomment-392792025  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2018-06-05 15:11:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1111#issuecomment-394746940  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
