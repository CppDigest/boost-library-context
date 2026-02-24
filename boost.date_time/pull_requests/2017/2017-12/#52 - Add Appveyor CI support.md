# #52 Add Appveyor CI support [Merged]

> Username: jeking3  
> Created at: 2017-12-21 00:49:45 UTC  
> Updated at: 2017-12-26 01:14:04 UTC  
> Merged at: 2017-12-22 13:28:37 UTC  
> Closed at: 2017-12-22 13:28:37 UTC  
> Url: https://github.com/boostorg/date_time/pull/52  

Results (with all test enabled): https://ci.appveyor.com/project/jeking3/date-time/build/1.0.2-develop  
  
Wow, Trac has 149 open issues for date_time...

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-12-22 15:02:03 UTC  
> Url: https://github.com/boostorg/date_time/pull/52#issuecomment-353614585  

Will you set up an appveyor account for this repository?  The way appveyor handles it seems pretty strange, and I don't know if the account is tied to boostorg or to the individual who sets it up as a repository admin.   
 If you don't want to manage it, I can do it, but I would need to request maintainer status to do it, which I suppose I could do since format and uuid are in order. :)

---

## Comment 2

> Username: eldiener  
> Created_at: 2017-12-22 21:10:26 UTC  
> Url: https://github.com/boostorg/date_time/pull/52#issuecomment-353675067  

I was able to add date_time to Appveyor. It shows up under my Appveyor account.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-12-22 21:25:40 UTC  
> Url: https://github.com/boostorg/date_time/pull/52#issuecomment-353677018  

I don't see it kicking off a build: https://github.com/boostorg/date_time/pull/57

---

## Comment 4

> Username: eldiener  
> Created_at: 2017-12-23 20:08:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/52#issuecomment-353745518  

It shows up under my appveyor account that it did and was successful.

---

## Comment 5

> Username: jeking3  
> Created_at: 2017-12-24 16:24:08 UTC  
> Url: https://github.com/boostorg/date_time/pull/52#issuecomment-353792797  

You want to enable the boostorg/date_time under your account, not eldiener/date_time.  I still don't see them running on pull requests, for example from yesterday: https://github.com/boostorg/date_time/pull/58

---

## Comment 6

> Username: eldiener  
> Created_at: 2017-12-24 17:18:14 UTC  
> Url: https://github.com/boostorg/date_time/pull/52#issuecomment-353795049  

All I know is that when I bring up Appveyor with my account login I see date_time because I added it. I am not sure what you mean by "your account" versus eldiener/date_time.

---

## Comment 7

> Username: jeking3  
> Created_at: 2017-12-25 01:15:38 UTC  
> Url: https://github.com/boostorg/date_time/pull/52#issuecomment-353811760  

It's the difference between adding your personal fork or the boostorg fork.

---

## Comment 8

> Username: eldiener  
> Created_at: 2017-12-25 01:25:27 UTC  
> Updated_at: 2017-12-25 01:25:59 UTC  
> Url: https://github.com/boostorg/date_time/pull/52#issuecomment-353812330  

I added the boost version of date_time to the Appveyor tests. All tests I have added to Appveyor under my login to Appveyor are of Boost libraries. Why am I supposed to fork a library and add it to Appveyor ? Am I supposed to login to Appveyor under some sort of Boost account ? If so how do I do that ?

---

## Comment 9

> Username: jeking3  
> Created_at: 2017-12-25 01:30:00 UTC  
> Url: https://github.com/boostorg/date_time/pull/52#issuecomment-353812654  

If you have admin access to the boostorg/date_time repository, it should show up in your appveyor account.. it'll look like this (sorry for the resolution, my laptop screen is 4KUHD):  
![image](https://user-images.githubusercontent.com/11771245/34330332-fe499ba2-e8e8-11e7-9dbf-9971a070180d.png)

---

## Comment 10

> Username: eldiener  
> Created_at: 2017-12-25 02:31:54 UTC  
> Url: https://github.com/boostorg/date_time/pull/52#issuecomment-353817245  

It shows up in my Appveyor account as another project which is run whenever date_time on Github gets updated. I do not see the Boostorg icon you are showing above. I just see a list of projects. All the projects listed are for the public Boostorg version of a library on Github and not for any of my own personal libraries on Github. Maybe I am supposed to sign on to Appveyor using some sort of Boostorg sign on but I do not know what that is.

---

## Comment 11

> Username: jeking3  
> Created_at: 2017-12-25 04:19:44 UTC  
> Url: https://github.com/boostorg/date_time/pull/52#issuecomment-353827896  

I think someone needs to add you as an admin on that repository in order to see it.

---

## Comment 12

> Username: eldiener  
> Created_at: 2017-12-25 06:43:56 UTC  
> Url: https://github.com/boostorg/date_time/pull/52#issuecomment-353837795  

I can see it fine when I am logged in to Appveyor. I do not understand what you are saying. You seem to think I have do something so that you know the result of the Appveyor run, but I do not know what I am supposed to do since I can see the result whenever I go to my Appveyor account online.

---

## Comment 13

> Username: jeking3  
> Created_at: 2017-12-25 15:05:06 UTC  
> Url: https://github.com/boostorg/date_time/pull/52#issuecomment-353874739  

Since you don't see date_time under "boostorg" then you are probably just getting branch update builds on your fork.

---

## Comment 14

> Username: eldiener  
> Created_at: 2017-12-26 01:14:04 UTC  
> Url: https://github.com/boostorg/date_time/pull/52#issuecomment-353900194  

I already told you that I do not have a fork of date_time.

---
