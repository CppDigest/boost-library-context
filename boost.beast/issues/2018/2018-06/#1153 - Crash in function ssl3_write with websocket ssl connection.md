# #1153 - Crash in function ssl3_write with websocket ssl connection [Closed]

> Username: nguoithichkhampha  
> Created at: 2018-06-07 03:20:49 UTC  
> Updated at: 2018-07-16 07:49:02 UTC  
> Closed at: 2018-07-16 07:49:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1153  

### Version of Beast  
Beast 79  
  
### Steps necessary to reproduce the problem  
Hi @vinniefalco , We implemented beast library on our product and got some crash reports from customer.  
Many crash from function ssl3_write of openssl library. Can you give some suggestion for root cause of this issue ?  
I attached stack call and local variable related to beast library  
![Figure 1-1](https://scontent.fhan5-1.fna.fbcdn.net/v/t1.0-9/34584750_10210007320018038_2741275628429901824_n.jpg?_nc_cat=0&oh=d21920a6f3d8180df787592017e832d8&oe=5BBE622F "Figure 1-1")  
  
This is variable from frame stack 02  
![Figure 1-1](https://scontent.fhan5-1.fna.fbcdn.net/v/t1.0-9/34815555_10210007319898035_6780135063951507456_n.jpg?_nc_cat=0&oh=e92a6cb97ccd969e19d43146b7e36f5e&oe=5BC11BD1 "Figure 1-1")

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-07 05:19:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1153#issuecomment-395296279  

I would suggest using Boost 1.66.0 or Boost 1.67.0 and the version of Beast that comes with it. Beast version 79 is quite old and contains bugs.

---

## Comment 2

> Username: nguoithichkhampha  
> Created at: 2018-06-07 06:49:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1153#issuecomment-395312190  

after update beast this issue will be resolved ? I'm not sure this issue from beast library or we are using the library wrong way.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-06-07 11:50:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1153#issuecomment-395392827  

Version 79 is a beta version and contains known crashing defects, so it is very possible that simply updating will resolve your issue. However, if the issue is not resolved upon updating I would be happy to look at your code to see if I can find the problem.

---

## Comment 4

> Username: nguoithichkhampha  
> Created at: 2018-06-07 17:02:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1153#issuecomment-395493755  

Great, thanks for your support.

---

## Comment 5

> Username: TechnikEmpire  
> Created at: 2018-06-09 07:06:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1153#issuecomment-395946507  

If upgrading doesn't resolve this, are IO calls   
(which I assume are async) completion handlers on the encrypted stream wrapped in an explicit strand?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2018-07-09 07:22:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1153#issuecomment-403384111  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: stale[bot]  
> Created at: 2018-07-16 07:49:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1153#issuecomment-405170914  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
