# #714 - Where is the Boost Folder? [Open]

> Username: theMilliP  
> Created at: 2022-11-24 17:20:54 UTC  
> Updated at: 2023-03-31 09:11:43 UTC  
> Url: https://github.com/boostorg/boost/issues/714  

Hello, I had an old version of boost and it was working fine. But now there is no boost folder in the repo? What is the include path and where are the .hpp files please?

---

## Comment 1

> Username: mclow  
> Created at: 2022-11-24 18:15:12 UTC  
> Url: https://github.com/boostorg/boost/issues/714#issuecomment-1326753074  

This sounds like a great question for the [boost-users mailing list](boost-users@lists.boost.org)  
  
Subscription info here: https://lists.boost.org/mailman/listinfo.cgi/boost-users

---

## Comment 2

> Username: theMilliP  
> Created at: 2022-11-24 18:18:43 UTC  
> Url: https://github.com/boostorg/boost/issues/714#issuecomment-1326755128  

I will subscribe but could you please help me move on? I am adding my email to the list but please if you know why help?

---

## Comment 3

> Username: mclow  
> Created at: 2022-11-24 18:19:18 UTC  
> Url: https://github.com/boostorg/boost/issues/714#issuecomment-1326755469  

Did you read how to download boost at https://www.boost.org/users/history/version_1_80_0.html ?

---

## Comment 4

> Username: theMilliP  
> Created at: 2022-11-24 18:24:10 UTC  
> Updated at: 2022-11-24 18:24:53 UTC  
> Url: https://github.com/boostorg/boost/issues/714#issuecomment-1326758006  

I need to add it as a submodule, so I don't just want to download the folders/files. I want to clone the repo so I can update it later in the future. In the website there is only the folders with no version control. I have no choice but download the folders? If that's the case could you please confirm and I will download them with no git.  
I have subscribed :)

---

## Comment 5

> Username: mclow  
> Created at: 2022-11-24 18:25:13 UTC  
> Url: https://github.com/boostorg/boost/issues/714#issuecomment-1326758563  

Then you should take a loot at https://github.com/boostorg/wiki/wiki/Getting-Started%3A-Overview

---

## Comment 6

> Username: mclow  
> Created at: 2022-11-24 18:26:00 UTC  
> Url: https://github.com/boostorg/boost/issues/714#issuecomment-1326758971  

`git clone --recursive` is what you're looking for, I think.

---

## Comment 7

> Username: theMilliP  
> Created at: 2022-11-24 18:30:36 UTC  
> Url: https://github.com/boostorg/boost/issues/714#issuecomment-1326761404  

I see thanks so much trying. Not sure this adds it as submodule though. I could try ```git submodule add --recursive```

---

## Comment 8

> Username: theMilliP  
> Created at: 2022-11-24 19:22:28 UTC  
> Url: https://github.com/boostorg/boost/issues/714#issuecomment-1326790090  

Unfortunately this does not work. Is there a way I can add boost recursively as a submodule so the boost folder is there please?

---

## Comment 9

> Username: theMilliP  
> Created at: 2022-11-24 19:32:41 UTC  
> Url: https://github.com/boostorg/boost/issues/714#issuecomment-1326793978  

Could please someone help? I am pretty sure someone knows this by heart, please just shoot a word or two?

---

## Comment 10

> Username: mclow  
> Created at: 2022-11-24 20:33:00 UTC  
> Url: https://github.com/boostorg/boost/issues/714#issuecomment-1326821177  

You seem to be under a misconception.  
A small number of people read these bug reports.  Today (Thanksgiving), it's probably just me.  
  
The boost-users mailing list (see above) has almost 4700 subscribers.

---

## Comment 11

> Username: ycollet  
> Created at: 2023-03-31 09:11:42 UTC  
> Url: https://github.com/boostorg/boost/issues/714#issuecomment-1491600751  

There are some big differences between the boost-1.81 downloaded from boost.org and the one downloaded from github release page:  
On github release page, 1.81 has cmake files but no boost directory with the headers only part,  
On boost.org 1.81 has no cmake files but have the boost directory.  
How can I regenerate this boost directory when using boost from github ?
