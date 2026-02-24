# #3 - Hash function needs a salt [Closed]

> Username: vinniefalco  
> Created at: 2019-10-04 19:01:14 UTC  
> Updated at: 2020-05-02 02:40:16 UTC  
> Closed at: 2020-05-02 02:40:16 UTC  
> Url: https://github.com/boostorg/json/issues/3  

This needs a good hash function for strings, and we should probably guard against algorithmic complexity attacks by allowing for a salt.

---

## Comment 1

> Username: Knackie  
> Created at: 2019-10-09 08:51:46 UTC  
> Url: https://github.com/boostorg/json/issues/3#issuecomment-539905305  

i can help u :)

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-10-09 10:47:18 UTC  
> Url: https://github.com/boostorg/json/issues/3#issuecomment-539947325  

Okay, well... don't keep me in suspense! What did you have in mind?

---

## Comment 3

> Username: Knackie  
> Created at: 2019-10-09 11:04:52 UTC  
> Url: https://github.com/boostorg/json/issues/3#issuecomment-539952949  

probably use std::hash  
Or bash command

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-10-09 11:09:04 UTC  
> Url: https://github.com/boostorg/json/issues/3#issuecomment-539954172  

`std::hash` doesn't have a salt

---

## Comment 5

> Username: Knackie  
> Created at: 2019-10-09 11:11:30 UTC  
> Url: https://github.com/boostorg/json/issues/3#issuecomment-539954848  

or with hashlib, if  you define a string as salt  
  
![image](https://user-images.githubusercontent.com/43431712/66476586-4bd8bc80-ea96-11e9-99db-02e51a92c523.png)

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-10-09 11:26:16 UTC  
> Url: https://github.com/boostorg/json/issues/3#issuecomment-539959151  

Is that C++? SHA256 looks a bit too strong for a hash function used to drive an unordered map...

---

## Comment 7

> Username: Knackie  
> Created at: 2019-10-09 12:07:50 UTC  
> Url: https://github.com/boostorg/json/issues/3#issuecomment-539971897  

u can use different SHA like SHA1  
  
Le mer. 9 oct. 2019 à 13:26, Vinnie Falco <notifications@github.com> a  
écrit :  
  
> Is that C++? SHA256 looks a bit too strong for a hash function used to  
> drive an unordered map...  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/vinniefalco/json/issues/3?email_source=notifications&email_token=AKLLOIBZTZ3VTEOVH5IEWZDQNW5VRA5CNFSM4I5TIJF2YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOEAXR63Y#issuecomment-539959151>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AKLLOIA4YYCJIJHVTKLMDI3QNW5VRANCNFSM4I5TIJFQ>  
> .  
>

---

## Comment 8

> Username: 0xdead4ead  
> Created at: 2019-10-15 14:08:57 UTC  
> Url: https://github.com/boostorg/json/issues/3#issuecomment-542231104  

How about looking an easy implementation PBKDF2?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-10-15 17:00:02 UTC  
> Url: https://github.com/boostorg/json/issues/3#issuecomment-542309982  

> PBKDF2  
  
We don't need cryptographic strength here

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-10-15 17:00:53 UTC  
> Url: https://github.com/boostorg/json/issues/3#issuecomment-542310336  

Something from here would be appropriate:  
https://github.com/pdimov/hash2/tree/master/include/boost/hash2

---

## Comment 11

> Username: vinniefalco  
> Created at: 2020-05-02 01:13:31 UTC  
> Url: https://github.com/boostorg/json/issues/3#issuecomment-622647374  

We can use the address of the implementation as the salt, since that doesn't change until a rehash.
