# #24 This comparison was always false [Merged]

> Username: mikekaganski  
> Created at: 2018-05-17 00:17:08 UTC  
> Updated at: 2018-08-22 15:23:54 UTC  
> Merged at: 2018-08-22 15:23:54 UTC  
> Closed at: 2018-08-22 15:23:54 UTC  
> Url: https://github.com/boostorg/locale/pull/24  



---

## Comment 1

> Username: artyom-beilis  
> Created_at: 2018-08-22 06:53:33 UTC  
> Url: https://github.com/boostorg/locale/pull/24#issuecomment-414930522  

I don't see a bug there. The check is that country name is always capital latter.  
  
So if something there is not letter (i.e. if it is lower case it is converted to upper and if it is not a upper stop)  
  
I don't see it as a bug.  
  
Can you show an example it causes an issue?

---

## Comment 2

> Username: mikekaganski  
> Created_at: 2018-08-22 13:08:32 UTC  
> Url: https://github.com/boostorg/locale/pull/24#issuecomment-415025508  

I am sorry, but I don't have an example how would this be a problem, since I found it reading the code. Still, the code is either wrong, or just unreachable: there's no such tmp [i] so that condition `if(tmp[i] < 'a' && 'z' < tmp[i])` could be satisfied. If its value is less than 'a', then it cannot be simultaneously be greater than 'z'. The same is true for the second place.

---

## Comment 3

> Username: mikekaganski  
> Created_at: 2018-08-22 13:11:54 UTC  
> Url: https://github.com/boostorg/locale/pull/24#issuecomment-415026500  

And to do the check that you mention ("The check is that country name is always capital latter"), the change I propose is required to exit early

---

## Comment 4

> Username: artyom-beilis  
> Created_at: 2018-08-22 15:23:05 UTC  
> Url: https://github.com/boostorg/locale/pull/24#issuecomment-415071759  

Ahhh... I got it.

---
