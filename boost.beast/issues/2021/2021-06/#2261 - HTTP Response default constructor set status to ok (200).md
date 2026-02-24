# #2261 - HTTP Response default constructor set status to ok (200) [Closed]

> Username: GorbachevDaniil  
> Created at: 2021-06-15 17:57:04 UTC  
> Updated at: 2021-06-15 18:57:52 UTC  
> Closed at: 2021-06-15 18:57:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2261  

### Version of Beast 313  
  
Hello! I have small question.  
I want to understand why when http response just created it's already has status ok (200)?  
I see it's coming from default constructor for message class, but it's not clear behaviour to have 200 for response when no request being sent.  
  
Best regards,  
Gorbachev Daniil.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-06-15 17:58:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2261#issuecomment-861715867  

Would you prefer the value be uninitialized or undefined?

---

## Comment 2

> Username: GorbachevDaniil  
> Created at: 2021-06-15 18:00:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2261#issuecomment-861717570  

I see that you have unknown in status enums, I think it's best option for this

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-06-15 18:02:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2261#issuecomment-861718863  

What would `result_int()` return, then? https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__http__header/result_int.html  
  
And why would this be better than the current implementation?

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-06-15 18:14:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2261#issuecomment-861727160  

I think the short answer is that when sending a response, 200 OK is the most likely code to be used, so if it makes sense to default it to anything, it would be this.

---

## Comment 5

> Username: GorbachevDaniil  
> Created at: 2021-06-15 18:23:54 UTC  
> Updated at: 2021-06-15 18:29:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2261#issuecomment-861732919  

Okay, thank you for your answers! So do I understand right that in most cases server will return 200 and because of it default status is 200? It's looks like an attempt to predicting the future. If status will be unknown as default then it will return 0 in `result_int()` method till real response was arrived and it's looks fine to me, but of course I could be wrong, because I'm not an expert in beast library.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-06-15 18:50:38 UTC  
> Updated at: 2021-06-15 18:51:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2261#issuecomment-861749731  

>  It's looks like an attempt to predicting the future  
  
No, the goal of the constructor is to put as many members into a known, valid state as it is possible to do so practically.

---

## Comment 7

> Username: GorbachevDaniil  
> Created at: 2021-06-15 18:57:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2261#issuecomment-861754449  

Okay, thanks.
